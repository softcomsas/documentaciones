# Funcionalidad: Actualizar Log Legacy

> **Módulo:** [[modulo-logs]]
> **CMD:** `logs.legacy.update`
> **Tipo:** Log / Evento / RPC emit (fire-and-forget)

## Descripción funcional

Completa el ciclo de vida de un log registrado previamente con `logs.legacy.create`, agregando la respuesta obtenida, el código HTTP y el estado final (SUCCESS, ERROR, TIMEOUT).

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `api` | `TLogsLegacyAPI` | Sí | Origen: LEGACY_PANEL o LEGACY_DESCARGAS |
| `hash` | `string` | Sí | Identificador de correlación (debe coincidir con el `create`) |
| `response` | `unknown` | Sí | Respuesta obtenida del procesamiento |
| `code` | `number` | Sí | Código HTTP de la respuesta |
| `user` | `string` | No | Usuario que realizó el request (si aplica) |

## Respuesta

Sin respuesta (emit — fire-and-forget).

## Riesgos

- ⚠️ Si el `hash` no coincide con ningún log existente, el update se pierde silenciosamente.
- ⚠️ El estado final (SUCCESS/ERROR/TIMEOUT) lo determina `ms-logs` según el `code` — la lógica de mapeo no está documentada aquí.

## Archivos fuente relevantes

- `src/contracts/logs/interfaces/legacy.ts` (interfaz `update`)
