# Core vs. Custom Dependencies

> **Última revisión:** 2026-04-29

## Frontend Angular / TypeScript

| Dependencia | Tipo | Categoría | Alternativa moderna | Estado |
|---|---|---|---|---|
| `@angular/core` v16 | Core framework | Core vendor | Angular v17+ (Signals) | 🟡 EOL Nov 2024 — actualizar |
| `@angular/material` v16 | UI components | Core vendor | Angular Material v17+ | 🟡 Vinculado a Angular v16 |
| `@angular/cdk` v16 | Utilidades UI | Core vendor | CDK v17+ | 🟡 Ídem |
| `@angular/router` v16 | Routing | Core vendor | v17+ (functional guards) | 🟡 Ídem |
| `@angular/forms` v16 | Formularios | Core vendor | v17+ | 🟡 Ídem |
| `@angular-architects/module-federation` v15 | Module Federation | Core vendor | v18 (native federation) | 🟡 Vigente para v16 |
| `@ngxs/store` v3.8 | State management | Core vendor | NGXS v4+ o NgRx Signals | 🟢 Vigente |
| `@ngreat/until-destroy` v9 | RxJS lifecycle | Customización | takeUntilDestroyed (Angular 16+) | 🟡 Tiene alternativa nativa |
| `@ngx-translate/core` v14 | i18n | Customización | Angular i18n nativo o transloco | 🟢 Vigente |
| `date-fns` v2 | Fechas | Core vendor | date-fns v3+ o Temporal API | 🟡 v2 en maintenance |
| `apexcharts` v3 | Charts | Customización | Chart.js, D3 | 🟢 Vigente |
| `angular-calendar` v0.31 | Calendario UI | Customización | Sin estándar de vendor | 🟢 Vigente |
| `@vex/` (tema) | UI theme | 🔴 Customización embebida | Extraer a lib shared | 🔴 Copiado en `main/` y `auth-app/` |
| `lodash` v4 | Utilidades JS | Core vendor | Métodos nativos ES2022+ | 🟡 En mantenimiento mínimo |
| `highlight.js` v11 | Syntax highlight | Customización | Prism.js | 🟢 Vigente |
| `exceljs` v4 | Excel generation | Customización | SheetJS (xlsx) | 🟢 Vigente |
| `@iconify/*` | Iconos | Customización | Material Symbols, Lucide | 🟢 Vigente |
| `ionic/angular` v7 | Mobile framework | Core vendor | Ionic v8+ | 🟡 Verificar compatibilidad con Angular v16 |

## Backend NestJS

| Dependencia | Tipo | Categoría | Alternativa moderna | Estado |
|---|---|---|---|---|
| `@nestjs/core` v10 | Core framework | Core vendor | NestJS v11 | 🟢 Vigente |
| `@nestjs/sequelize` v10 | ORM | Core vendor | TypeORM, Drizzle, Prisma | 🟢 Vigente |
| `@nestjs/swagger` v7 | API docs | Core vendor | — | 🟢 Vigente |
| `@nestjs/schedule` v4 | Cron | Core vendor | — | 🟢 Vigente |
| `@nestjs/axios` v3 | HTTP client | Core vendor | Fetch nativo (Node 18+) | 🟢 Vigente |
| `class-validator` v0.14 | Validación DTO | Core vendor | Zod (alternativa moderna) | 🟢 Vigente |
| `class-transformer` v0.5 | Serialización | Core vendor | — | 🟢 Vigente |
| `jsonwebtoken` v9 | JWT validation | Customización | `@nestjs/jwt` (más idiomático) | 🟡 Funcional pero no usa el módulo oficial NestJS |
| `exiftool-vendored` v30 | Metadatos archivos | Customización | Sharp (para imágenes) | 🟡 Dependencia nativa (binario exiftool) |
| `file-type` v16 (CJS) | Detección MIME | Customización | `mime-types` | 🟡 v16 es CJS; v19+ es ESM |
| `axios` v1 | HTTP client | Core vendor | Fetch nativo | 🟡 Redundante con @nestjs/axios |

## Backend Legacy Yii2 PHP

| Dependencia | Tipo | Categoría | Alternativa moderna | Estado |
|---|---|---|---|---|
| Yii2 framework | Core framework | 🔴 Legacy vendor | Laravel 11, Symfony 7 | 🔴 En maintenance; Yii3 aún no estable |
| PHP (versión ⚠️ sin verificar) | Runtime | Core vendor | PHP 8.3 | 🔴 Si < 8.1 está en EOL |
| Codeception | Testing | Core vendor | PHPUnit + Behat | 🟢 Vigente |
| Bower (`.bowerrc`) | Frontend pkg mgr | 💀 Deprecado | npm/pnpm/yarn | 🔴 Bower está abandonado desde 2017 |

## Resumen de Riesgos

| Riesgo | Severidad | Módulos afectados |
|---|---|---|
| Angular v16 EOL | 🔴 | Todos los MFEs |
| Node.js 18 EOL | 🔴 | Todo el frontend |
| Yii2 legacy sin migración | 🔴 | logistica, oferta, documentacion |
| Bower en backends Yii2 | 🔴 | logistica, oferta, documentacion |
| PHP versión sin verificar | 🔴 | logistica, oferta, documentacion |
| Vex theme duplicado | 🟡 | main, auth-app |
| `file-type` v16 CJS | 🟡 | descargas-app backend |
| `jsonwebtoken` sin módulo NestJS oficial | 🟡 | descargas-app backend |
