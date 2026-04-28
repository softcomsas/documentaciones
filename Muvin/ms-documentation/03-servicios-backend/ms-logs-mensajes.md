---
tags: [servicios, TCP, logs, mensajes]
última-revisión: 2026-04-27
---

# Mensajes TCP — MsLogs

> **Tipo de comunicación:** Emit (fire and forget) + Send (request/response)
> **Consumido por:** [[modulo-contracts]]
> **Fuente de patrones:** `src/common/cmd/constant.ts` → `CMDS.logs`

## `logs.legacy.create` — Emit

**Propósito:** Registrar el inicio de una llamada a un sistema legacy
**Patrón CMD:** `CMDS.logs.legacy.create`
**Tipo:** Fire and forget (no espera respuesta)

| Campo | Obligatorio | Tipo | Descripción |
|-------|:-----------:|------|-------------|
| `api` | ✅ | `TLogsLegacyAPI` | Sistema legacy (`LEGACY_PANEL` o `LEGACY_DESCARGAS`) |
| `hash` | ✅ | `string` | Identificador único de la llamada |
| `user` | ❌ | `number` | ID del usuario (opcional) |
| `method` | ✅ | `TMethod` | Verbo HTTP de la llamada |
| `endpoint` | ✅ | `string` | Ruta del endpoint llamado |
| `payload` | ✅ | `unknown` | Cuerpo de la solicitud |

## `logs.legacy.update` — Emit

**Propósito:** Registrar el resultado de una llamada a un sistema legacy
**Patrón CMD:** `CMDS.logs.legacy.update`
**Tipo:** Fire and forget

| Campo | Obligatorio | Tipo | Descripción |
|-------|:-----------:|------|-------------|
| `api` | ✅ | `TLogsLegacyAPI` | Sistema legacy |
| `hash` | ✅ | `string` | Hash del registro a actualizar |
| `response` | ✅ | `unknown` | Respuesta recibida |
| `code` | ✅ | `number` | Código HTTP de respuesta |
| `user` | ❌ | `number` | ID del usuario (opcional) |

## `logs.legacy.search.id` — Send

**Propósito:** Buscar un log de legacy por ID y API
**Patrón CMD:** `CMDS.logs.legacy.search.id`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ id: number, api: TLogsLegacyAPI }` | Criterios de búsqueda |
| Salida | `TApi<{ id: number } \| null>` | ID del log si existe |

## `logs.legacy.search.user` — Send

**Propósito:** Buscar logs de legacy por usuario y criterios
**Patrón CMD:** `CMDS.logs.legacy.search.user`

| Campo | Tipo |
|-------|------|
| Entrada | `{ api, method, endpoint, createdAt, code }` |
| Salida | `TApi<null>` (🚧 respuesta pendiente de definir) |

## `logs.legacy.search.terms` — Send

**Propósito:** Búsqueda de logs por términos
**Patrón CMD:** `CMDS.logs.legacy.search.terms`

| Campo | Tipo |
|-------|------|
| Entrada | `{ api: TLogsLegacyAPI }` |
| Salida | `TApi<null>` (🚧 respuesta pendiente de definir) |
