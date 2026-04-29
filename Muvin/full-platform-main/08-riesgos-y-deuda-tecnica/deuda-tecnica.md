# Deuda Técnica

> **Última revisión:** 2026-04-29
> **Priorización:** Impacto (Alto/Medio/Bajo) × Esfuerzo (Alto/Medio/Bajo)

## Tabla Priorizada

| # | Ítem | Impacto | Esfuerzo | Prioridad | Módulos afectados |
|---|---|---|---|---|---|
| DT-01 | Actualizar Angular de v16 → v17/v18 (EOL) | Alto | Alto | 🔴 Urgente | Todos los frontends |
| DT-02 | Actualizar Node.js de v18 → v20 LTS | Alto | Bajo | 🔴 Urgente | Todo el monorepo |
| DT-03 | Implementar backend real para auth-app | Alto | Medio | 🔴 Urgente | auth-app |
| DT-04 | Verificar y sanear `local.env` de Yii2 (posibles credenciales en git) | Alto | Bajo | 🔴 Urgente | logistica, oferta, documentacion |
| DT-05 | Documentar y asegurar CronController (auth en endpoints HTTP de cron) | Alto | Bajo | 🔴 Urgente | descargas-app |
| DT-06 | Migrar backends Yii2 a NestJS (logística, oferta, documentación) | Alto | Muy alto | 🟡 Planificar | logistica, oferta, documentacion |
| DT-07 | Centralizar Vex theme en `shared/` (actualmente duplicado) | Medio | Bajo | 🟡 Próximo sprint | main, auth-app |
| DT-08 | Reemplazar `jsonwebtoken` directo por `@nestjs/jwt` | Medio | Bajo | 🟡 Próximo sprint | descargas-app backend |
| DT-09 | Mover `NoHtmlPipe` a archivo de pipes dedicado | Bajo | Bajo | 🟢 Backlog | descargas-app backend |
| DT-10 | Reemplazar `console.error` por logger estructurado en controllers | Medio | Bajo | 🟡 Próximo sprint | descargas-app backend |
| DT-11 | Actualizar `date-fns` v2 → v3 | Bajo | Bajo | 🟢 Backlog | Todos los frontends |
| DT-12 | Reemplazar `@ngreat/until-destroy` por `takeUntilDestroyed` nativo (Angular 16+) | Bajo | Medio | 🟢 Backlog | Todos los frontends |
| DT-13 | Eliminar Bower y `.bowerrc` de backends Yii2 | Bajo | Bajo | 🟡 Próximo sprint | logistica, oferta, documentacion |
| DT-14 | Eliminar `Vagrantfile` de backends Yii2 si no se usa en producción | Bajo | Bajo | 🟢 Backlog | logistica, oferta, documentacion |
| DT-15 | Verificar y documentar el propósito de `contacts/` en el shell | Bajo | Bajo | 🟢 Backlog | main |
| DT-16 | Actualizar Nx de v16 → v19 (incremental) | Medio | Alto | 🟡 Planificar | Monorepo completo |
| DT-17 | Usar `takeUntilDestroyed` y Signals de Angular en nuevos desarrollos | Bajo | Bajo | 🟢 Backlog | Todos los frontends nuevos |
| DT-18 | Resolver solapamiento `OfertaController` (logistica vs oferta-app) | Medio | Medio | 🟡 Próximo sprint | logistica, oferta |
| DT-19 | Revisar LoggingInterceptor — verificar que no logea datos sensibles | Alto | Bajo | 🔴 Urgente | descargas-app backend |
| DT-20 | Verificar versión de PHP de backends Yii2 (riesgo EOL) | Alto | Bajo | 🔴 Urgente | logistica, oferta, documentacion |

## Resumen por Categoría

| Categoría | Cantidad de ítems |
|---|---|
| 🔴 Urgente | 7 |
| 🟡 Planificar / Próximo sprint | 7 |
| 🟢 Backlog | 6 |
