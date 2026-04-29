# Stack Tecnológico — muvin-app

## Frontend

| Tecnología | Versión | Uso |
|---|---|---|
| Angular | 21.0.3 | Framework principal |
| Angular Material | 21.0.2 | Componentes UI (tablas, formularios, snackbar) |
| Angular CDK | 21.0.2 | Primitivas de UI (overlay, drag & drop) |
| RxJS | 7.8.2 | Programación reactiva, observables |
| TypeScript | 5.9.3 | Tipado estático |
| `@angular/localize` | 21.0.3 | Internacionalización i18n (ES / EN) |

## Patrones de Angular utilizados

| Patrón | Descripción |
|---|---|
| **Standalone Components** | Sin NgModules; todos los componentes son standalone |
| **Signals** | Estado reactivo local con `signal()`, `computed()`, `effect()` |
| **Lazy Loading** | Todas las rutas cargan módulos con `loadComponent` / `loadChildren` |
| **Inject function** | `inject()` en constructor (no `@Inject` ni constructor injection legacy) |
| **OnPush** | `ChangeDetectionStrategy.OnPush` en todos los componentes |
| **takeUntilDestroyed** | Gestión de suscripciones con `DestroyRef` |

## Herramientas de desarrollo

| Herramienta | Versión | Uso |
|---|---|---|
| Angular CLI | 21.0.2 | Build, serve, i18n |
| ESLint | 9.39.1 | Linting |
| Prettier | 3.7.4 | Formateo de código |
| angular-eslint | 21.0.1 | Reglas específicas de Angular |

## UI Kit interno

La aplicación consume un UI Kit propio con alias `@ui` y `@ui-services`:
- `UiLinkComponent`, `UiButtonComponent`, `UiButtonIconComponent`
- `UiCtrlDateStrategyComponent`
- `UiIconComponent`
- `UiSnackBarService`

## Alias de path (tsconfig)

| Alias | Apunta a |
|---|---|
| `@core` | `src/app/core/_index.ts` |
| `@assignment` | `src/app/.../assignment/_index.ts` |
| `@api-queries` | `src/app/core/services/api/queries/_index.ts` |
| `@api-types` | `src/app/core/services/api/types/` |
| `@ui` | UI Kit externo |
| `@ui-services` | Servicios del UI Kit |
| `@ui-types` | Tipos del UI Kit |
