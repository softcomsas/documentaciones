# F-04 — Actualizar CTG (Timbúes)

> **Módulo:** [[modulo-timbues]]
> **Tipo:** 🔌 Integración
> **Endpoint de entrada:** `POST /timbues/timbues/actualizar-ctg`

## Descripción funcional

Actualiza el **CTG (Código de Trazabilidad de Granos)** de un camión que ingresa al Puerto Timbúes. Las credenciales del usuario del puerto viajan cifradas con AES-128-ECB y son descifradas por el bus antes de autenticarse.

## Flujo principal

```mermaid
flowchart TD
    A([POST /timbues/timbues/actualizar-ctg]) --> B[Recibir body con user+password AES cifrados]
    B --> C[openssl_decrypt user AES-128-ECB passOpenSSL]
    C --> D[openssl_decrypt password AES-128-ECB passOpenSSL]
    D --> E[LoginTimbues::getInstance\nusuario + password + urlBase]
    E --> F{¿Login OK?}
    F -->|No| G[Retornar statusCode + logs]
    F -->|Sí| H[instance.actualizarCtg body + param]
    H --> I[POST Puerto Timbúes\ntramitesenlinea.com.ar/puertos/muvin]
    I --> J[Retornar status + mensaje + logs]
```

## Servicios backend invocados

| Verbo | Ruta | Descripción |
|---|---|---|
| POST | `https://puerto.tramitesenlinea.com.ar/puertos/muvin/[accion]` | Actualización de CTG |

## Payload esperado

```json
{
  "user": "<AES-128-ECB cifrado>",
  "password": "<AES-128-ECB cifrado>",
  "ctg": "...",
  "patente": "...",
  "...": "otros campos del CTG"
}
```

## Riesgos

- 🔴 Sin reintentos si el puerto no responde
- 🟡 Un fallo en el descifrado AES retorna `statusCode` de error sin indicar claramente que fue un problema de credenciales
