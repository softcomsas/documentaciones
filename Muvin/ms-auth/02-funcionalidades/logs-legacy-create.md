# Funcionalidad: Crear Log Legacy

> **Módulo:** [[modulo-logs]]
> **CMD:** `logs.legacy.create`
> **Tipo:** Log / Evento / RPC emit (fire-and-forget)

## Descripción funcional

Registra la entrada de un request proveniente del sistema legacy. Se invoca al inicio del procesamiento de un request, antes de tener la respuesta. El log queda en estado `PENDING` hasta que se llame a `logs.legacy.update`.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `api` | `TLogsLegacyAPI` | Sí | Origen: LEGACY_PANEL o LEGACY_DESCARGAS |
| `hash` | `string` | Sí | Identificador de correlación del request |
| `user` | `string` | No | Usuario que realiza el request (si aplica) |
| `method` | `TMethod` | Sí | Verbo HTTP del request original |
| `endpoint` | `string` | Sí | Ruta del endpoint invocado |
| `payload` | `unknown` | Sí | Cuerpo del request |

## Respuesta

Sin respuesta (emit — fire-and-forget).

## Riesgos

- ⚠️ Si `ms-logs` está caído, el emit se pierde sin notificar al caller.
- ⚠️ El campo `payload` es `unknown` — no hay tipado del contenido del log.

## Archivos fuente relevantes

- `src/contracts/logs/interfaces/legacy.ts` (interfaz `create`)
