# Dominio: Assignment (Asignación de Cupos)

## Descripción

Dominio activo principal de `muvin-app`. Permite visualizar cupos disponibles y solicitudes pendientes en una **grilla semanal**, aplicar filtros, y ejecutar asignaciones manuales o automáticas.

## Ruta

`/quota-operations/assignment`

## Estructura de archivos

```
assignment/
├── container/container.ts          ← AssignmentContainer (providedIn: container)
├── pages/
│   ├── home/                       ← Vista principal (grilla semanal)
│   │   ├── view/view.ts            ← AssignmentHomeView
│   │   └── components/
│   │       ├── management/         ← Selector de fecha + botones de navegación
│   │       ├── preview/            ← Grilla semanal (matrix producto × día)
│   │       ├── grid-quotas/        ← Tabla de cupos
│   │       └── grid-requests/      ← Tabla de solicitudes
│   ├── filter/
│   │   ├── view/view.ts            ← AssignmentFilterView
│   │   └── components/             ← Filtros: producto, zona, destino, corredor, etc.
│   └── management/
│       ├── view/view.ts            ← AssignmentManagementView
│       └── components/             ← Formulario de asignación individual
├── service.ts                      ← AssignmentService (estado global del dominio)
├── types.ts                        ← TData, TFacets, TFiltered, TTerm, TViews, etc.
├── constants.ts
├── functions.ts
├── routes.ts
└── _index.ts
```

## AssignmentService — Signals

```typescript
// Estado de carga
public readonly loading: Signal<boolean>

// Catálogos
public readonly products: Signal<IOption<number>[]>
public readonly headboards: Signal<...>
public readonly rejections: Signal<...>

// Grilla semanal
public readonly start: WritableSignal<Date>   // fecha inicial editable
public readonly days: Signal<Date[]>          // 5 días calculados desde start
```

## Páginas del dominio

### Home (`/assignment`)
Vista principal con 4 sub-componentes:
- **ManagementComponent** — selector de rango de fecha, links a filter/management
- **PreviewComponent** — grilla semanal agrupada por `product` o `zone` (5 columnas = 5 días, rows = grupos)
- **GridQuotasComponent** — tabla detallada de cupos del grupo/día seleccionado
- **GridRequestsComponent** — tabla detallada de solicitudes del grupo/día seleccionado

### Filter (`/assignment/filter`)
Pantalla de filtros avanzados:
- **Facets:** products, zones, destinations, receivers, commercialSenderSecondaryOnes, brokers, clients, recipients
- Al aplicar → actualiza `AssignmentService.filtered`

### Management (`/assignment/management`)
Pantalla de asignación:
- Asignación manual: relaciona un cupo con una solicitud
- Asignación automática: `post-legacy-assign-auto`
- Rechazo de solicitudes: `post-legacy-rechazar-solicitudes`
- Edición de aplicación: `post-legacy-edit-application`

## Queries HTTP utilizadas

| Query key | Método | Descripción |
|---|---|---|
| `get-legacy-listado-zona` | GET | Lista cupos y solicitudes por zona/fecha |
| `get-legacy-buscar-para-asignar` | GET | Busca coincidencias para asignación |
| `get-legacy-cabecera` | GET | Datos de cabecera del cupo |
| `get-legacy-centro-producto` | GET | Centro-producto |
| `get-legacy-zona-centro` | GET | Zonas disponibles |
| `get-legacy-motivos-rechazo` | GET | Motivos de rechazo |
| `post-legacy-assign-auto` | POST | Asignación automática |
| `post-legacy-assign-quotas` | POST | Asignar cupos |
| `post-legacy-assign-requests` | POST | Asignar solicitudes |
| `post-legacy-rechazar-solicitudes` | POST | Rechazar solicitudes |
| `post-legacy-edit-application` | POST | Editar aplicación |
| `post-legacy-delete-cupos` | POST | Eliminar cupos |
| `put-legacy-cupo` | PUT | Actualizar cupo |
| `get-identity-list` | GET | Identidades / usuarios |
| `get-identity-one` | GET | Detalle de identidad |
