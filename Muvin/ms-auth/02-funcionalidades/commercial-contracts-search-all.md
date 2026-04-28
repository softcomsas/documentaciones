# Funcionalidad: Listado Paginado de Contratos

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.search.all`
> **Tipo:** Consulta / Reporte / RPC send

## Descripción funcional

Retorna un listado paginado de contratos con filtros opcionales. Es la funcionalidad base para pantallas de listado y reportes de contratos comerciales.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `company` | `number` | Sí | ID de la compañía |
| `page` | `number` | Sí | Número de página (IPagination) |
| `limit` | `number` | Sí | Registros por página (IPagination) |
| `client` | `number` | No | Filtro por cliente |
| `status` | `TCommercialContractStatus` | No | Filtro por estado |
| `code` | `number` | No | Filtro por código |

## Respuesta esperada

```typescript
{ success: true, data: IContract[], meta: { total: number } }
```

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `search-all`)
- `src/common/interfaces/pagination.ts` (IPagination)
