# Hotspots — Archivos y Módulos de Mayor Riesgo

> **Última revisión:** 2026-04-29

## Hotspots por Complejidad / Concentración de Lógica

| Archivo | Módulo | Motivo | Severidad |
|---|---|---|---|
| `descargas-app/backend/src/controllers/descargas.controller.ts` | descargas-app | 268 líneas, múltiples endpoints, `NoHtmlPipe` declarada inline, manejo de errores heterogéneo | 🟡 |
| `descargas-app/backend/src/services/descargas.service.ts` | descargas-app | Servicio principal de negocio de todo el módulo; concentra lógica de cupos, AFIP y DB | 🔴 |
| `descargas-app/backend/src/services/external.api.service.ts` | descargas-app | Integración con AFIP y APIs externas; punto único de falla para todo el módulo | 🔴 |
| `descargas-app/backend/src/services/cron.service.ts` | descargas-app | Tareas automáticas programadas con impacto en DB; errores pueden ser silenciosos | 🟡 |
| `logistica-app/backend/api/source/controllers/ViajeController.php` | logistica-app | Controlador central de logística; presumiblemente el más complejo del backend Yii2 | 🟡 |
| `documentacion-app/backend/api/source/controllers/DocumentoController.php` | documentacion-app | Manejo de archivos adjuntos + estados de presentación | 🟡 |
| `main/src/app/app.routes.ts` | main (shell) | Define el routing de todos los MFEs; un error aquí rompe toda la plataforma | 🔴 |
| `shared/data-access-user/` | shared | Estado global de usuario; un bug aquí afecta todos los MFEs simultáneamente | 🟡 |
| `shared/frontend/auth/` | shared | Guards y servicios de auth compartidos; impacto en toda la plataforma | 🟡 |

## Hotspots por Deuda Tecnológica

| Área | Módulos afectados | Severidad |
|---|---|---|
| Angular 16 EOL (Nov 2024) | Todos los frontends | 🔴 |
| Node.js 18 EOL (Apr 2025) | Todo el monorepo | 🔴 |
| Yii2 PHP backends sin migración planificada | logistica, oferta, documentacion | 🔴 |
| Bower en Yii2 (abandonado desde 2017) | logistica, oferta, documentacion | 🔴 |
| Vex theme duplicado (main + auth-app) | main, auth-app | 🟡 |
| `@ngreat/until-destroy` en lugar de `takeUntilDestroyed` nativo | Todos los MFEs | 🟡 |

## Hotspots por Incertidumbre (Sin Documentar)

| Área | Módulo | Descripción |
|---|---|---|
| Backend de autenticación | auth-app | El backend es un stub. No se sabe dónde ocurre la autenticación real. |
| Propósito de `contacts/` en el shell | main | No está claro su función de negocio |
| CronJob: qué hace y cuándo | descargas-app | El cron service existe pero su lógica no está documentada |
| `UsuarioEntidadHijaController` | documentacion-app | Jerarquía de entidades no documentada |
| `RecursosExternosController` | documentacion-app | Propósito no claro |
| Relación entre `OfertaController` en logistica y `oferta-app` | ambos | Posible duplicidad de lógica |
