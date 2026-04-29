# Riesgos y Deuda Técnica — muvin-app

## Deuda técnica actual

| # | Ítem | Impacto | Prioridad |
|---|---|---|---|
| DT-01 | Módulos `quotas` y `requests` con rutas comentadas — sin implementar | Alto: funcionalidad incompleta | Alta |
| DT-02 | Auth / login comentado — el token se gestiona externamente (sin flujo claro en este repo) | Alto: dependencia opaca | Alta |
| DT-03 | `ENVIRONMENT` hardcodeado a `development.ts` — no usa `environment.prod.ts` en imports | Medio: riesgo de URL incorrecta en prod | Media |
| DT-04 | `isRefreshing` y `refreshSubject` como variables de módulo en el interceptor (no en servicio) | Bajo: estado global no encapsulado | Baja |
| DT-05 | `console.log(window.navigator.language)` en login (código de debug) — **en app-panel**, no en muvin-app | Bajo | Baja |
| DT-06 | Comentarios `// eslint-disable @typescript-eslint/no-magic-numbers` en queries — constantes sin nombre | Bajo: legibilidad | Baja |

## Riesgos operativos

| # | Riesgo | Probabilidad | Mitigación |
|---|---|---|---|
| R-01 | Backend legado (`ms-legacy`) sin versionar API — cambios rompen queries | Media | Documentar contratos; agregar tests de integración |
| R-02 | Caché en memoria (`Map`) se pierde en cada recarga de página | Baja | Aceptable; TTL corto por defecto |
| R-03 | Refresh token vencido sin logout claro — usuario queda en loop | Baja | Agregar redirect a login al fallar refresh |
| R-04 | i18n incompleto — algunos strings no marcados con `$localize` | Baja | Auditoría periódica con `ng extract-i18n` |

## Mejoras propuestas

| # | Mejora | Beneficio |
|---|---|---|
| M-01 | Implementar módulos `quotas` y `requests` (descomentar y completar rutas + páginas) | Completar MVP |
| M-02 | Mover `isRefreshing` / `refreshSubject` a un `AuthService` dedicado | Mejor encapsulamiento |
| M-03 | Agregar `environment.prod.ts` en build configuration de Angular | Correctitud en producción |
| M-04 | Agregar tests unitarios para `AssignmentService` (Signals + forkJoin) | Confiabilidad |
| M-05 | Implementar logout automático al fallar el refresh | UX y seguridad |
| M-06 | Persistir filtros activos en `sessionStorage` para no perderlos al navegar | UX |
