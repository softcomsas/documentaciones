# Funcionalidad: Buscar por Referencia y Compañía

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.search.reference`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Busca contratos a partir de una referencia y una compañía. Similar a `search-one` pero posiblemente retorna múltiples resultados si una referencia puede estar asociada a más de un contrato.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `reference` | `string` | Sí | Referencia del contrato |
| `company` | `number` | Sí | ID de la compañía |

## Respuesta esperada

```typescript
{ success: true, data: IContract | IContract[] }
// ⚠️ Pendiente de verificar si retorna uno o varios
```

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `search-reference`)
