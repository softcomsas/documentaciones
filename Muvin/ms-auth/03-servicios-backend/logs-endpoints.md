# Endpoints: Logs Legacy (consumidos)

> **Microservicio destino:** ms-logs
> **Transporte:** TCP
> **Última revisión:** 2026-04-27

---

## `logs.legacy.create`

**Propósito:** Registrar entrada de request legacy
**Tipo:** RPC emit (fire-and-forget) | **Detalle:** [[logs-legacy-create]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `api` | `TLogsLegacyAPI` | Sí |
| `hash` | `string` | Sí |
| `user` | `string` | No |
| `method` | `TMethod` | Sí |
| `endpoint` | `string` | Sí |
| `payload` | `unknown` | Sí |

---

## `logs.legacy.update`

**Propósito:** Completar log con respuesta obtenida
**Tipo:** RPC emit (fire-and-forget) | **Detalle:** [[logs-legacy-update]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `api` | `TLogsLegacyAPI` | Sí |
| `hash` | `string` | Sí |
| `response` | `unknown` | Sí |
| `code` | `number` | Sí |
| `user` | `string` | No |

---

## `logs.legacy.search.id`

**Propósito:** Buscar log por ID
**Tipo:** RPC send | **Detalle:** [[logs-legacy-search-id]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `id` | ⚠️ pendiente | Sí |

---

## `logs.legacy.search.user`

**Propósito:** Buscar logs por usuario
**Tipo:** RPC send | **Detalle:** [[logs-legacy-search-user]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `user` | `string` | Sí |

---

## `logs.legacy.search.terms`

**Propósito:** Búsqueda textual sobre logs
**Tipo:** RPC send | **Detalle:** [[logs-legacy-search-terms]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| ⚠️ Pendiente de verificar | — | — |
