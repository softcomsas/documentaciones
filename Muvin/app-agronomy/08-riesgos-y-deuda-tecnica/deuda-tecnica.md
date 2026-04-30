# Deuda Técnica — App Agronomy

> **Última revisión:** 2026-04-30

Lista priorizada de deuda técnica detectada. Ordenada por impacto/esfuerzo.

## Alta prioridad (impacto alto, esfuerzo bajo-medio)

| ID | Problema | Impacto | Esfuerzo | Archivo(s) |
|----|---------|---------|---------|------------|
| DT-01 | Bug `=` en lugar de `===` en login | 🔴 Seguridad | Bajo | `auth.service.ts:36,55` |
| DT-02 | Expiración de token desactivada en Guard | 🔴 Seguridad | Bajo | `centro-auth.guard.ts` |
| DT-03 | `console.log` con datos de choferes en producción | 🔴 Seguridad | Bajo | `chofer.service.ts:18` |
| DT-04 | Token en `localStorage` (XSS risk) | 🔴 Seguridad | Alto | Toda la capa de auth |
| DT-05 | `@angular/flex-layout` deprecado | 🟡 Mantenibilidad | Medio | Varios templates |
| DT-06 | `angular-in-memory-web-api` en dependencias de prod | 🟡 Funcional | Bajo | `package.json` |

## Media prioridad (impacto medio, esfuerzo medio)

| ID | Problema | Impacto | Esfuerzo | Archivo(s) |
|----|---------|---------|---------|------------|
| DT-07 | jQuery en proyecto Angular | 🟡 Mantenibilidad | Medio | `package.json` + usos |
| DT-08 | Modelos duplicados (`camion.ts`, `chofer.ts`) en pedidos y shared | 🟡 Mantenibilidad | Medio | `pages/pedidos/models/`, `shared/models/` |
| DT-09 | `EventEmitter` usado como bus de eventos inter-servicios | 🟡 Correctitud | Medio | `reservas.service.ts` |
| DT-10 | Roles hardcodeados numéricamente | 🟡 Mantenibilidad | Bajo | Guard + servicios |
| DT-11 | Construcción manual de URLs (sin `HttpParams`) | 🟡 Seguridad/Mantenibilidad | Medio | `reservas.service.ts` |
| DT-12 | Métodos legacy comentados en servicios | 🟡 Deuda acumulada | Bajo (eliminar) | `pedidos.service.ts`, `camion.service.ts` |
| DT-13 | `moment.js` en uso (deprecado) | 🟡 Mantenibilidad | Medio | Varios servicios |
| DT-14 | `ng-recaptcha` desactualizado | 🟡 Seguridad | Bajo | `package.json` |

## Baja prioridad

| ID | Problema | Impacto | Esfuerzo |
|----|---------|---------|---------|
| DT-15 | `quill` v1.x EOL | 🟢 Funcional | Medio |
| DT-16 | `perfect-scrollbar` sin mantenimiento | 🟢 UI | Bajo |
| DT-17 | Sin store centralizado (NGXS/NgRx) | 🟢 Escalabilidad | Alto |
| DT-18 | Typo en nombre de archivo `exel.service.ts` | 🟢 Cosmético | Bajo |
