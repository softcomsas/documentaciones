# F-01 — Grilla Semanal de Cupos y Solicitudes

## Descripción

Vista principal del dominio `assignment`. Muestra en una **tabla tipo matriz** (grupos × días) la cantidad de cupos disponibles y solicitudes pendientes para los próximos 5 días, con posibilidad de agrupar por producto o por zona.

## Ruta

`/quota-operations/assignment` (home)

## Componentes involucrados

| Componente | Responsabilidad |
|---|---|
| `AssignmentHomeView` | Layout principal, coordina sub-componentes |
| `ManagementComponent` | Selector de fecha de inicio, botones de navegación a Filter y Management |
| `PreviewComponent` | Grilla semanal: filas = grupos (producto/zona), columnas = días (d0–d4) + totales |
| `GridQuotasComponent` | Tabla detallada de cupos del grupo/día seleccionado |
| `GridRequestsComponent` | Tabla detallada de solicitudes del grupo/día seleccionado |

## Flujo de la vista

```
1. AssignmentService carga datos con get-legacy-listado-zona
2. start signal = TODAY por defecto
3. days computed = [d0, d1, d2, d3, d4] desde start
4. PreviewComponent computa IRow[] agrupando cupos y solicitudes
5. Cada celda ICell = { quotas: {items, count}, requests: {items, count} }
6. Usuario hace clic en celda → GridQuotas / GridRequests se actualizan
```

## Estructura de la grilla

```
        | d0 (hoy) | d1 | d2 | d3 | d4 | Disponibles | Solicitados
─────────────────────────────────────────────────────────────────────
SOJA    |  3/2     | .. | .. | .. | .. |     12      |     8
MAÍZ    |  1/0     | .. | .. | .. | .. |      5      |     3
─────────────────────────────────────────────────────────────────────
        Cupos / Solicitudes por celda
```

## State del componente (AssignmentService)

```typescript
start: WritableSignal<Date>       // controlado por ManagementComponent
days: Signal<Date[]>              // [d0, d1, d2, d3, d4]
```

## Agrupación (Term)

- `product` — agrupa filas por producto
- `zone` — agrupa filas por zona
- Controlado por `TTerm.key` en `AssignmentService`

## Vistas individuales (TView)

Cada grilla (quotas / requests) tiene dos modos:
- `INDIVIDUAL` — muestra cada registro por separado
- `GROUPED` — agrupa registros similares
