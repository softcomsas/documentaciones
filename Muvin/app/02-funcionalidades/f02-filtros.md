# F-02 — Filtros Avanzados

## Descripción

Pantalla de filtrado que permite al usuario segmentar los cupos y solicitudes visibles en la grilla semanal. Los filtros se basan en **facets** calculadas a partir de los datos cargados.

## Ruta

`/quota-operations/assignment/filter`

## Facets disponibles

| Facet | Tipo | Descripción |
|---|---|---|
| `products` | `IOption<number>[]` | Productos disponibles |
| `zones` | `IOption<number>[]` | Zonas disponibles |
| `destinations` | `IOption[]` | Destinos |
| `receivers` | `IOption[]` | Receptores |
| `commercialSenderSecondaryOnes` | `IOption[]` | Remitentes comerciales secundarios |
| `brokers` | `IOption[]` | Corredores |
| `clients` | `IOption[]` | Clientes |
| `recipients` | `IOption[]` | Destinatarios |

## TFiltered — Estado del filtro activo

```typescript
interface Filtered {
  products: number[];
  zones: number[];
  destinations: string[];
  receivers: string[];
  commercialSenderSecondaryOnes: string[];
  brokers: string[];
  clients: string[];
  recipients: string[];
}
```

## Flujo

```
1. Usuario abre /assignment/filter
2. Facets calculadas a partir de TData (cupos y solicitudes cargados)
3. Usuario selecciona valores en cada facet
4. Al aplicar → AssignmentService.filtered actualiza el signal
5. PreviewComponent re-computa IRow[] con datos filtrados
6. Router navega back a /assignment (home)
```
