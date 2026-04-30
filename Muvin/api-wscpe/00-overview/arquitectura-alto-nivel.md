# Arquitectura de Alto Nivel — api-wscpe

> [[README]] · [[vision-general]]

## Diagrama de componentes

```mermaid
graph TD
    subgraph "Consumidores"
        ApiMain["api (NestJS)"]
        ApiBus["api-bus (PHP)"]
        Admin["Admin\n(Swagger UI)"]
    end

    subgraph "api-wscpe (Yii2 Basic)"
        Web["web/index.php\n(Entry Point)"]
        Auth["BearerAuth\n(token en BD)"]
        LoginCtrl["LoginController\nPOST /login/login"]
        CpeCtrl["CpeController\nPOST /cpe"]
        CertCtrl["CertificadoController\nPOST /certificado"]
        UserCtrl["UserController\nPOST /user/create-user"]
        SwaggerCtrl["SwaggerController\nGET /swagger"]

        QueryCache["QueryCache\n(cache de CTGs)"]
        AfipWS["Afipws.php\n(SOAP client wrapper)"]
        Certificado["Certificado\n(cert + key en BD)"]
        UserModel["User\n(auth token MD5)"]
    end

    subgraph "AFIP Webservices"
        WSAA["WSAA\n(Autenticación AFIP)"]
        WSCPE_TEST["wscpe QA\ncpea-ws-qaext.afip.gob.ar"]
        WSCPE_PROD["wscpe PROD\ncpea-ws.afip.gob.ar"]
    end

    DB[("MySQL\nquery_cache\ncertificado\nuser")]

    ApiMain --> Web
    ApiBus --> Web
    Admin --> Web

    Web --> Auth
    Auth --> LoginCtrl
    Auth --> CpeCtrl
    Auth --> CertCtrl
    Auth --> UserCtrl
    Auth --> SwaggerCtrl

    CpeCtrl --> QueryCache
    QueryCache --> AfipWS
    AfipWS --> Certificado
    Certificado --> DB
    AfipWS --> WSAA
    WSAA -->|TA token + sign| AfipWS
    AfipWS --> WSCPE_TEST
    AfipWS --> WSCPE_PROD
    QueryCache --> DB
    UserModel --> DB
```

## Flujo de una consulta CPE

```mermaid
sequenceDiagram
    participant Cliente
    participant CpeCtrl as CpeController
    participant QueryCache
    participant Afipws
    participant BD
    participant AFIP

    Cliente->>CpeCtrl: POST /cpe {cuit, ctg} + Bearer token
    CpeCtrl->>QueryCache: buscarCtg(ctg, cuit, userId)
    QueryCache->>BD: SELECT WHERE ctg=X AND cuit_consulta=Y

    alt Cache HIT
        BD-->>QueryCache: registro encontrado
        QueryCache-->>CpeCtrl: resultado cacheado
        CpeCtrl-->>Cliente: JSON con datos CPE
    else Cache MISS
        BD-->>QueryCache: null
        QueryCache->>Afipws: consultarCtg(ctg, cuit, userId)
        Afipws->>BD: SELECT certificado WHERE id_user=1
        BD-->>Afipws: cert + key + passphrase
        Afipws->>AFIP: SOAP GetServiceTA("wscpe")
        AFIP-->>Afipws: {token, sign}
        Afipws->>AFIP: SOAP ConsultarCPEAutomotor({token, sign, cuit, ctg})
        AFIP-->>Afipws: datos CPE (sin PDF)
        Afipws-->>QueryCache: array CPE
        QueryCache->>BD: INSERT query_cache
        QueryCache-->>CpeCtrl: resultado fresco
        CpeCtrl-->>Cliente: JSON con datos CPE
    end
```

## Wrapper de respuesta global

Todas las respuestas de la API son envueltas automáticamente en `config/web.php` (`beforeSend`):

```json
{
  "success": true,
  "status": 200,
  "data": { ... }
}
```

Excepto la respuesta del `SwaggerController` que devuelve HTML.

## Entornos AFIP

| Parámetro `ENTORNO_WS` | WSAA | WSCPE |
|------------------------|------|-------|
| `TEST` (default) | homologación | `cpea-ws-qaext.afip.gob.ar` |
| `PROD` | producción | `cpea-ws.afip.gob.ar` |
