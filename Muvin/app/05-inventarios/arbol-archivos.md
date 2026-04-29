# Inventario — Árbol de Archivos

> **Última revisión:** 2026-04-29

```
app/
├── package.json                    ← muvin-app, Angular 21
├── angular.json
├── tsconfig.json
├── eslint.config.js
├── nginx.conf
├── proxy.conf.json
└── src/
    ├── index.html
    ├── main.ts
    ├── environments/
    │   └── development.ts          ← ENVIRONMENT.api (URL del backend)
    └── app/
        ├── app.ts                  ← bootstrap, providers, interceptors
        ├── app.html
        ├── config.ts
        ├── routes.ts               ← APP_ROUTES
        ├── core/
        │   ├── _index.ts
        │   ├── constants/          ← ZERO, ONE, TWO, FOUR, FIVE, TODAY, CACHE, etc.
        │   ├── functions/          ← generateControlFn, normalizeDateToTimestampFn, etc.
        │   ├── interceptors/
        │   │   └── token.ts        ← tokenInterceptorFn (JWT + refresh)
        │   ├── interfaces/
        │   │   ├── api-response.ts ← TApi<T>
        │   │   ├── option.ts       ← IOption<T>
        │   │   ├── option-extended.ts
        │   │   ├── list.ts
        │   │   ├── page.ts
        │   │   ├── pagination.ts
        │   │   ├── strict.ts
        │   │   └── _index.ts
        │   ├── routes/
        │   │   ├── private.ts      ← PRIVATE_ROUTES (estructura de rutas tipada)
        │   │   ├── prefix-private.ts ← PREFIX_PRIVATE_ROUTES()
        │   │   └── _index.ts
        │   ├── services/
        │   │   ├── api/
        │   │   │   ├── service.ts  ← ApiService (queries + caché)
        │   │   │   ├── interfaces/
        │   │   │   ├── types/
        │   │   │   └── queries/
        │   │   │       ├── _index.ts (QUERIES_MAP)
        │   │   │       ├── get-identity-list.ts
        │   │   │       ├── get-identity-one.ts
        │   │   │       ├── get-legacy-buscar-para-asignar.ts
        │   │   │       ├── get-legacy-cabecera.ts
        │   │   │       ├── get-legacy-centro-producto.ts
        │   │   │       ├── get-legacy-listado-zona.ts
        │   │   │       ├── get-legacy-motivos-rechazo.ts
        │   │   │       ├── get-legacy-zona-centro.ts
        │   │   │       ├── post-legacy-assign-auto.ts
        │   │   │       ├── post-legacy-assign-quotas.ts
        │   │   │       ├── post-legacy-assign-requests.ts
        │   │   │       ├── post-legacy-delete-cupos.ts
        │   │   │       ├── post-legacy-edit-application.ts
        │   │   │       ├── post-legacy-rechazar-solicitudes.ts
        │   │   │       └── put-legacy-cupo.ts
        │   │   ├── breadcrumb/
        │   │   └── config/
        │   │       └── service.ts  ← ConfigService (token, refresh, taxId)
        │   └── types/
        ├── shared/
        │   ├── components/
        │   └── _index.ts
        ├── public/
        │   └── container/          ← PublicContainer (rutas públicas — comentadas)
        ├── private/
        │   ├── routes.ts           ← DECLARATION_PRIVATE_ROUTES
        │   ├── container/          ← PrivateContainer
        │   ├── home/               ← PrivateHomePage
        │   └── modules/
        │       ├── configuration/
        │       │   └── routes.ts
        │       └── quota-operations/
        │           ├── routes.ts   ← DECLARATION_QUOTA_OPERATIONS_ROUTES
        │           ├── container/  ← QuotaOperationsContainer
        │           ├── home/       ← QuotaOperationHomePage
        │           └── domains/
        │               ├── assignment/
        │               │   ├── _index.ts
        │               │   ├── service.ts       ← AssignmentService
        │               │   ├── types.ts
        │               │   ├── constants.ts
        │               │   ├── functions.ts
        │               │   ├── routes.ts
        │               │   ├── container/
        │               │   └── pages/
        │               │       ├── home/
        │               │       │   ├── view/
        │               │       │   └── components/
        │               │       │       ├── management/
        │               │       │       ├── preview/
        │               │       │       ├── grid-quotas/
        │               │       │       ├── grid-requests/
        │               │       │       └── common.scss
        │               │       ├── filter/
        │               │       │   ├── view/
        │               │       │   └── components/
        │               │       └── management/
        │               │           ├── view/
        │               │           └── components/
        │               ├── quotas/
        │               │   └── routes.ts        ← pendiente
        │               └── requests/
        │                   └── routes.ts        ← pendiente
        └── temp/
```
