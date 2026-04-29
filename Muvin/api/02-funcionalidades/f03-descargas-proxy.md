# F-03: Proxy de descargas

> **Módulo:** temporary
> **Protocolo:** REST
> **Criticidad:** 🟡 Media

---

## Descripción

Permite a los clientes solicitar la generación o descarga de archivos a través de `descargas-app`. muvin-api actúa como intermediario autenticado: obtiene un token, lo adjunta al request y reenvía el body original a la aplicación de descargas.

---

## Endpoint

### POST `/api/descargas`

**Body:** `unknown` — se reenvía tal cual a `descargas-app`

**Response:** Datos / archivo retornado por `descargas-app`

---

## Flujo de autenticación de dos pasos

```mermaid
sequenceDiagram
    participant TS as TemporaryService
    participant L as /api/v1/login
    participant A as /api/backend/web/login/access-token

    TS->>L: POST {username: "30556991835", password: "Muvin2021*"}
    L-->>TS: {data: {authorization_code: "XYZ"}}
    TS->>A: POST {authorization_code: "XYZ", rol_id: 3}
    A-->>TS: {access_token: "Bearer ..."}
```

> ⚠️ Este doble paso de autenticación (login → access-token) ocurre **en cada request**. No hay caché del token.

---

## Flujo completo

```mermaid
flowchart TD
    A[POST /api/descargas\nbody] --> B[TemporaryController.create]
    B --> C[TemporaryService.query]
    C --> D[_getToken: login + access-token]
    D --> E{¿Token OK?}
    E -->|Error| F[_throwExternalError → HttpException]
    E -->|Token válido| G[POST descargas-app/descargas/\nbody + Bearer token]
    G --> H{¿Respuesta OK?}
    H -->|Error HTTP| F
    H -->|Éxito| I[Retornar result.data al cliente]
```

---

## Comportamiento ante errores

`_throwExternalError()` inspecciona el error Axios y:
- Si tiene `response.data` y `response.status` → lanza `HttpException(data, status)` — propaga el error del servicio externo
- En cualquier otro caso → lanza `HttpException({message: 'External service unavailable'}, 502)`

---

## Advertencias

| Ítem | Riesgo |
|------|--------|
| Credenciales hardcodeadas | 🔴 Crítico — USER/PASSWORD en texto plano en código |
| Sin caché de token | 🟡 Performance — 2 requests HTTP por cada solicitud |
| Sin timeout | 🟡 Riesgo — puede bloquear indefinidamente |
| `AROUND = 'panel'` | 🟡 — Apunta siempre a prod; no hay forma de cambiar entorno sin recompilar |

---

## Referencias

- [[modulo-temporary]]
- [[deuda-tecnica]]
