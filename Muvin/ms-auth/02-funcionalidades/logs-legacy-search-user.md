# Funcionalidad: Buscar Logs por Usuario

> **Módulo:** [[modulo-logs]]
> **CMD:** `logs.legacy.search.user`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Retorna el historial de logs asociados a un usuario específico. Útil para auditorías de actividad por usuario.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `user` | `string` | Sí | Identificador del usuario |
| `page` | `number` | ⚠️ Pendiente | Paginación (⚠️ Pendiente de verificar si aplica) |
| `limit` | `number` | ⚠️ Pendiente | Paginación (⚠️ Pendiente de verificar si aplica) |

## Respuesta esperada

```typescript
{ success: true, data: ILogLegacy[] }
```

## Archivos fuente relevantes

- `src/contracts/logs/interfaces/legacy.ts` (interfaz `search-user`)
