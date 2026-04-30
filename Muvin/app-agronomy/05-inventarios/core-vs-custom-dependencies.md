# Dependencias Core vs. Custom — App Agronomy

> **Última revisión:** 2026-04-30

## Dependencias core del framework

| Paquete | Versión | Propósito | Alternativa moderna |
|---------|---------|-----------|---------------------|
| `@angular/core` | 16.2.x | Framework principal | — (está vigente) |
| `@angular/router` | 16.2.x | Enrutamiento | — |
| `@angular/forms` | 16.2.x | Formularios | — |
| `@angular/http` (via HttpClient) | 16.2.x | Peticiones HTTP | — |
| `@angular/service-worker` | 16.2.x | PWA | — |
| `rxjs` | ~7.5.0 | Reactividad | — |
| `typescript` | ~5.1.0 | Tipado | — |

## Dependencias de UI

| Paquete | Versión | Propósito | Estado | Riesgo |
|---------|---------|-----------|--------|--------|
| `@angular/material` | 16.2.x | Componentes Material | Vigente | 🟢 |
| `@angular/cdk` | 16.2.x | Base de componentes | Vigente | 🟢 |
| `@angular/flex-layout` | 15.0.0-beta.42 | Layout | ⚠️ **Deprecado** | 🔴 |
| `bootstrap` | 5.2.3 | CSS grid/utilities | Vigente | 🟢 |
| `perfect-scrollbar` | 1.5.5 | Scrollbar custom | Sin mantenimiento | 🟡 |

## Dependencias de visualización

| Paquete | Versión | Propósito | Estado | Riesgo |
|---------|---------|-----------|--------|--------|
| `apexcharts` + `ng-apexcharts` | 3.35.5 / 1.7.1 | Gráficos interactivos | Vigente | 🟢 |
| `echarts` + `ngx-echarts` | 5.3.3 / 14.0.0 | Gráficos avanzados | Vigente | 🟢 |
| `chart.js` + `ng2-charts` | 3.9.1 / 4.0.0 | Gráficos (secundario) | Vigente | 🟢 |
| `@angular/google-maps` | 16.2.14 | Mapas | Vigente | 🟢 |
| `angular-calendar` | 0.31.0 | Componente calendario | Vigente | 🟢 |

## Dependencias con riesgo elevado

| Paquete | Versión | Problema | Acción recomendada |
|---------|---------|----------|--------------------|
| `@angular/flex-layout` | beta.42 | Deprecado oficialmente | Migrar a CSS Flexbox nativo o Angular CDK Layout |
| `jquery` | 3.6.1 | Innecesario en Angular | Eliminar; reemplazar con APIs nativas del DOM |
| `moment` | 2.29.4 | Sin nuevas features | Migrar a `date-fns` (ya incluido) o `Temporal` API |
| `quill` | 1.3.7 | Versión 1.x EOL | Actualizar a Quill 2.x o reemplazar con otro editor |
| `ng-recaptcha` | 12.0.2 | Versión desactualizada | Actualizar a versión compatible con Angular 16 |
| `perfect-scrollbar` | 1.5.5 | Sin mantenimiento | Usar `overflow: auto` nativo o CDK ScrollDispatcher |

## Customizaciones propias

| Archivo | Propósito |
|---------|-----------|
| `webpack.config.js` | Config Webpack personalizada sobre Angular CLI (dev) |
| `webpack.prod.config.js` | Config Webpack personalizada para producción |
| `src/app/shared/inmemory-db/` | Base de datos en memoria (mocking de API) — 🚧 verificar si está activo |
| `nginx/default.conf` | Config Nginx custom para SPA |
| `ngsw-config.json` | Estrategia de caché PWA personalizada |
