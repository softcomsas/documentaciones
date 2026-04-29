# Flujo: Proxy AES + OAuth2 (Patrón SiloHub / Timbúes)

> **Aplica a:** módulos `silohub` y `timbues`
> **Última revisión:** 2026-04-29

---

## Descripción

El patrón central de api-bus para integraciones externas autenticadas:

1. El cliente envía parámetros **cifrados con AES-128-ECB**
2. api-bus los descifra con la clave configurada en `main.php`
3. api-bus obtiene un token OAuth2 (o equivalente) del sistema externo, con caché singleton
4. api-bus proxea el request al sistema externo
5. La respuesta se normaliza al formato `{success, status, data}`

---

## Diagrama de secuencia completo

```mermaid
sequenceDiagram
    autonumber
    participant APP as App/Panel
    participant MW as JWT Middleware
    participant PU as HtmlPurifier
    participant CTRL as Controller<br/>(AlgoritmoController)
    participant DEC as desencriptarParametros<br/>(AES-128-ECB)
    participant FAC as SiloHub::pasamanos
    participant AUTH as LoginSiloHub<br/>(singleton)
    participant EXT as SiloHub API<br/>(tomascasares.dyndns.org)

    APP->>MW: POST /silohub/algoritmo/... {Bearer JWT, body: {data: "AES_CIFRADO"}}
    MW->>MW: Validar JWT (usuario/roles)
    MW->>PU: beforeRequest — sanitizar params con HtmlPurifier
    PU->>CTRL: params sanitizados

    CTRL->>DEC: desencriptarParametros(params['data'])
    Note over DEC: openssl_decrypt(AES-128-ECB, passOpenSSL)
    DEC-->>CTRL: array de parámetros planos

    CTRL->>FAC: SiloHub::pasamanos(body, login, connection, url, method)

    alt Token en caché
        FAC->>AUTH: getInstance()->getToken()
        AUTH-->>FAC: token OAuth2 cacheado
    else Sin token o expirado
        FAC->>AUTH: getInstance()->getToken()
        AUTH->>EXT: POST /oauth/token {client_id, client_secret}
        EXT-->>AUTH: {access_token, expires_in}
        AUTH-->>FAC: access_token
    end

    FAC->>EXT: [method] [url] {Authorization: Bearer token, body}
    EXT-->>FAC: {data del sistema SiloHub}
    FAC-->>CTRL: respuesta cruda

    CTRL->>CTRL: beforeSend — normalizeResponse
    CTRL-->>APP: {success: true, status: 200, data: {...}}
```

---

## Puntos críticos de seguridad

| Punto | Riesgo | Descripción |
|-------|--------|-------------|
| `passOpenSSL` hardcoded | 🔴 Crítico | La clave AES está en `config/main.php` en texto plano |
| AES-128-ECB | 🔴 Crítico | ECB es determinístico; no provee confidencialidad real |
| `client_secret = 'secret'` | 🔴 Crítico | Credencial OAuth2 trivial |
| SSL deshabilitado | 🔴 Crítico | `verifyPeer=false`, `verifyHost=false` en `BaseCurl` |

---

## Flujo de tokens OAuth2 (SiloHub)

```mermaid
flowchart LR
    A[LoginSiloHub\ngetInstance] --> B{¿Token válido\nen memoria?}
    B -->|Sí| C[Retornar token]
    B -->|No| D[POST /oauth/token\nSiloHub API]
    D --> E[Guardar token\nen propiedad estática]
    E --> C
```

> ⚠️ El token se cachea **en memoria por request PHP**. Al ser PHP sin estado, en cada nuevo request HTTP se re-autentica con SiloHub. No hay caché persistente entre requests.

---

## Variante Timbúes

El flujo es análogo pero:
- La URL externa es `tramitesenlinea.com.ar/puertos/muvin`
- La autenticación usa `LoginTimbues` con su propio mecanismo
- La clave AES es diferente (`passOpenSSL` de Timbúes)

---

## Referencias

- [[modulo-silohub]]
- [[modulo-timbues]]
- [[stack-tecnologico]]
- [[security-inventory]]
