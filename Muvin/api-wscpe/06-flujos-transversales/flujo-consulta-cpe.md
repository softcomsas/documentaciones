# Flujo: Consulta CPE Completa

> [[_indice-flujos]] | Módulos: [[modulo-login]] → [[modulo-cpe]] → [[modulo-afipws]]

## Diagrama end-to-end

```mermaid
sequenceDiagram
    participant Cliente as Cliente\n(api / api-bus)
    participant Login as LoginController
    participant CpeCtrl as CpeController
    participant QCache as QueryCache
    participant Afipws
    participant BD as MySQL
    participant AFIP as AFIP WSCPE

    Note over Cliente: Paso 1: Obtener token (si no tiene uno vigente)
    Cliente->>Login: POST /login/login {username, password}
    Login->>BD: SELECT user WHERE username=X
    BD-->>Login: user record
    Login->>BD: UPDATE user SET token=md5(uniqid()), token_expire=now+9h
    BD-->>Login: OK
    Login-->>Cliente: {access_token, expires_at}

    Note over Cliente: Paso 2: Consultar CPE
    Cliente->>CpeCtrl: POST /cpe {cuit, ctg}\nAuthorization: Bearer token
    CpeCtrl->>BD: SELECT user WHERE token=X AND token_expire > now()
    BD-->>CpeCtrl: user válido (auth OK)

    CpeCtrl->>QCache: buscarCtg(ctg, cuit, 1)
    QCache->>BD: SELECT FROM query_cache WHERE ctg=X AND cuit_consulta=Y

    alt Cache HIT
        BD-->>QCache: resultado previo
        QCache->>QCache: verificar CUIT en result (recursivo)
        QCache-->>CpeCtrl: datos cacheados
        CpeCtrl-->>Cliente: {success:true, data:[{CPE...}]}
    else Cache MISS
        BD-->>QCache: null
        QCache->>Afipws: consultarCtg(ctg, cuit, userId=1)
        Afipws->>BD: SELECT certificado WHERE id_user=1
        BD-->>Afipws: {cert, key, passphrase, cuit}
        Afipws->>Afipws: write cert.key + key.pem to Afip_res/
        Afipws->>AFIP: GetServiceTA("wscpe") via WSAA
        AFIP-->>Afipws: {token, sign} (TA)
        Afipws->>AFIP: SOAP ConsultarCPEAutomotor({token,sign,cuit,ctg})
        AFIP-->>Afipws: datos CPE (stdClass)
        Afipws->>Afipws: unset(pdf) + convertir a array
        Afipws-->>QCache: array CPE
        QCache->>BD: INSERT INTO query_cache
        QCache-->>CpeCtrl: datos frescos
        CpeCtrl-->>Cliente: {success:true, data:[{CPE...}]}
    end
```

## Puntos de fallo

| Punto | Causa | Comportamiento |
|-------|-------|----------------|
| Token expirado | TTL 9h vencido | HTTP 401 |
| Certificado no cargado | BD `certificado` vacía | Exception en `Afipws::conectar()` |
| AFIP no disponible | WS caído | `data: ["Error en el WEBSERVICE - ..."]` con HTTP 200 |
| CUIT no delegado | AFIP rechaza consulta | `data: ["El cuit X no se puede consultar (no delegado)"]` |
| CTG inexistente | AFIP no encuentra el CTG | Respuesta de error de AFIP en `data` |
