# Endpoints REST — muvin-api

> **Base path global:** `/api`
> **Framework:** NestJS 11 + Express
> **Validación:** `ValidationPipe` global (class-validator)

---

## POST `/api/logs`

**Descripción:** Crea un nuevo log de auditoría. Fire-and-forget (no espera confirmación de ms-logs).

**Body:** `CreateLogBodyDTO`

**Response:** `200 void`

---

## PUT `/api/logs`

**Descripción:** Actualiza un log existente. Fire-and-forget.

**Body:** `UpdateLogBodyDTO`

**Response:** `200 void`

---

## GET `/api/logs/by-id`

**Descripción:** Busca un log específico por su ID.

**Query params:** `SearchIDLogQueryDTO`

| Param | Tipo | Descripción |
|-------|------|-------------|
| `id` | string | ID del log |

**Response:** `TLogLegacy | null`

---

## GET `/api/logs/by-user`

**Descripción:** Lista logs asociados a un usuario.

**Query params:** `SearchUserLogQueryDTO`

| Param | Tipo | Descripción |
|-------|------|-------------|
| `userId` | string | ID del usuario |

**Response:** `TLogLegacy | null`

---

## GET `/api/logs/by-terms`

**Descripción:** Búsqueda de logs por términos genéricos.

**Query params:** `SearchTermsLogQueryDTO`

**Response:** Array de logs matching los términos

---

## POST `/api/descargas`

**Descripción:** Proxy autenticado hacia `descargas-app`. El body se reenvía tal cual.

**Body:** `unknown` (any JSON payload)

**Response:** Datos retornados por `descargas-app`

**Errores:**

| Status | Descripción |
|--------|-------------|
| 4xx / 5xx | Propagado desde `descargas-app` |
| 502 | `descargas-app` no disponible o error inesperado |

---

## Comportamiento de errores silenciosos (logs)

Los endpoints del módulo `logs` capturan todas las excepciones con `try/catch` y retornan `undefined` en lugar de propagar el error. Esto es intencional para no bloquear al cliente por fallos de auditoría, pero **puede ocultar problemas**.

---

## Referencias

- [[modulo-logs]]
- [[modulo-temporary]]
- [[f02-logs-crud]]
- [[f03-descargas-proxy]]
