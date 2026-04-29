# Arquitectura General — muvin-app

## Diagrama de capas

```
┌────────────────────────────────────────────────────────┐
│                   Browser (SPA)                        │
├────────────────────────────────────────────────────────┤
│  APP_ROUTES                                            │
│  ├── /quota-operations  → PrivateContainer             │
│  │     └── /assignment  → AssignmentContainer          │
│  │           ├── (home)     AssignmentHomeView         │
│  │           ├── /filter    AssignmentFilterView       │
│  │           └── /management AssignmentManagementView  │
│  └── **  → redirect /quota-operations                  │
├────────────────────────────────────────────────────────┤
│  Core Layer (@core)                                    │
│  ├── ApiService       — queries tipadas + caché        │
│  ├── ConfigService    — token/refresh/taxId            │
│  └── tokenInterceptorFn — JWT + refresh automático     │
├────────────────────────────────────────────────────────┤
│  HTTP → API Backend (ENVIRONMENT.api)                  │
│  Queries: get-legacy-listado-zona, post-legacy-assign, │
│           get-identity-list, put-legacy-cupo, ...      │
└────────────────────────────────────────────────────────┘
```

## Arquitectura de rutas

```
APP_ROUTES
  └── PREFIX_PRIVATE_ROUTES()         ← string dinámico (i18n)
        └── PrivateContainer
              ├── (home)   PrivateHomePage
              └── quota-operations
                    └── QuotaOperationsContainer
                          ├── (home)  QuotaOperationHomePage
                          └── assignment
                                └── AssignmentContainer
                                      ├── (home)      AssignmentHomeView
                                      ├── filter      AssignmentFilterView
                                      └── management  AssignmentManagementView
```

## Patrón Container / View

Cada dominio sigue el patrón **Container → View → Components**:

```
AssignmentContainer     ← inyecta AssignmentService, gestiona providers
  └── AssignmentHomeView  ← coordina layout
        ├── ManagementComponent    ← controles de fecha + links
        ├── PreviewComponent       ← grilla semanal (Quotas + Requests)
        ├── GridQuotasComponent    ← tabla de cupos
        └── GridRequestsComponent  ← tabla de solicitudes
```

## Flujo de datos (Signals)

```
AssignmentService
  signals: loading, products, headboards, rejections
  signal:  start (fecha inicial)
  computed: days (5 fechas a partir de start)
       ↓
  PreviewComponent computed: rows (IRow[])
       ↓
  GridQuotasComponent / GridRequestsComponent @Input
```

## Comunicación HTTP

```
Component → AssignmentService.someAction()
               → ApiService.request('QUERY_KEY', payload)
                     → QUERIES_MAP.get(key) → lazy import query file
                           → HttpClient.post/get/put
                                → tokenInterceptorFn (adjunta JWT)
                                     → Backend API
```
