# Funcionalidad: Buscar Log por ID

> **Módulo:** [[modulo-logs]]
> **CMD:** `logs.legacy.search.id`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Recupera un registro de log específico a partir de su ID único.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `id` | `number` o `string` | Sí | ID del log (⚠️ Pendiente verificar tipo exacto) |

## Respuesta esperada

```typescript
{ success: true, data: ILogLegacy }
```

## Archivos fuente relevantes

- `src/contracts/logs/interfaces/legacy.ts` (interfaz `search-id`)
