# Arquitectura de Alto Nivel — api-bus

## Diagrama de capas

```mermaid
graph TD
    subgraph "Capa de entrada"
        HTTP[HTTP Request\nBearer JWT]
    end

    subgraph "Capa de autenticación"
        AUTH[ApiRestMuvinController\nHttpBearerAuth + CORS]
        COMMON_AUTH[common\models\User\nPersonaRol]
    end

    subgraph "Capa de módulos"
        SH[silohub\nAlgoritmoController\nAES decrypt + pasamanos]
        TI[timbues\nTimbuesController\nAES decrypt + actualizarCtg]
        VI[viterra\nDestinoController\nActiveRecord local]
        MO[monsanto\nLoginController\nAuth propia + CerealTrack]
        AQ[agroquimicos\nPedidoController\nZarcam + SanMiguel]
    end

    subgraph "Capa de componentes"
        BASECURL[BaseCurl\nHTTP cURL genérico]
        SILOHUB_C[SiloHub.php\nLoginSiloHub.php]
        TIMBUES_C[Timbues.php\nLoginTimbues.php]
        ZARCAM[Zarcam.php]
        SANMIGUEL[SanMiguel.php]
        CEREAL[CerealTrackConnect.php]
    end

    subgraph "Sistemas externos"
        EXT1[SiloHub API]
        EXT2[Puerto Timbúes]
        EXT3[Viterra DB local]
        EXT4[CerealTrack / Bayer]
        EXT5[Zarcam API]
        EXT6[San Miguel GPS]
    end

    HTTP --> AUTH --> COMMON_AUTH
    AUTH --> SH & TI & VI & MO & AQ
    SH --> SILOHUB_C --> BASECURL --> EXT1
    TI --> TIMBUES_C --> BASECURL --> EXT2
    VI --> EXT3
    MO --> CEREAL --> BASECURL --> EXT4
    AQ --> ZARCAM --> BASECURL --> EXT5
    AQ --> SANMIGUEL --> BASECURL --> EXT6
```

## Patrón de proxy AES

Los módulos `silohub` y `timbues` reciben las credenciales **cifradas con AES-128-ECB** desde el cliente. El bus las descifra usando su `passOpenSSL` interno (configurado en `main.php`) y las usa para autenticarse contra el sistema externo. Esto evita que las credenciales viajen en texto plano por la red interna.

```mermaid
sequenceDiagram
    participant C as Cliente Muvin
    participant B as api-bus
    participant E as Sistema Externo

    C->>B: POST /silohub/algoritmo/emitir-pedido-logistica\n{body, login: AES(creds), conexion: AES(creds)}
    B->>B: openssl_decrypt(login, AES-128-ECB, passOpenSSL)
    B->>B: openssl_decrypt(conexion, AES-128-ECB, passOpenSSL)
    B->>E: OAuth2 client_credentials → access_token
    E-->>B: access_token
    B->>E: POST /api/ConsultaSiloHub/GetEmitirPedidoLogistica\nAuthorization: Bearer {access_token}
    E-->>B: {statusCode, data}
    B-->>C: {success, status, data, logs}
```

## Formato de respuesta estándar

Todos los endpoints devuelven JSON normalizado por el `beforeSend` de Yii:

```json
{
  "success": true,
  "status": 200,
  "data": { ... }
}
```

En caso de error del proxy interno:

```json
{
  "status": 500,
  "mensaje": "Datos de conexión o login inválidos"
}
```
