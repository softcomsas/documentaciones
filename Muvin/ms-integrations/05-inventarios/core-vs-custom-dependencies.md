# Dependencias Core vs. Custom

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Dependencias core del framework/vendor

Estas son dependencias estándar de plataforma. No requieren lógica propia y tienen soporte activo del vendor.

| Paquete | Vendor | Propósito | Alternativa moderna | Bloqueante |
|---|---|---|---|---|
| `@nestjs/common` | NestJS | Decoradores, pipes, guards | N/A (es actual) | No |
| `@nestjs/core` | NestJS | Núcleo del framework | N/A | No |
| `@nestjs/microservices` | NestJS | Transporte TCP | N/A | No |
| `@nestjs/platform-express` | NestJS | Adaptador HTTP | ⚠️ No se usa HTTP, podría eliminarse | No |
| `@prisma/client` | Prisma | ORM cliente generado | N/A (es actual) | No |
| `reflect-metadata` | TC39 / polyfill | Decoradores TypeScript | Nativo en TS futuro | No |
| `rxjs` | ReactiveX | Observables (requerido por NestJS) | N/A | No |
| `class-validator` | typestack | Validación de DTOs | zod, joi | No |
| `class-transformer` | typestack | Serialización | zod | No |
| `joi` | hapi | Validación de schema env | zod, valibot | No |
| `dotenv` | Motdotla | Carga de `.env` | NestJS ConfigModule | No |

---

## Dependencias custom / de terceros con riesgo

| Paquete | Propósito | Estado | Alternativa recomendada | Riesgo |
|---|---|---|---|---|
| `bull` v4 | Sistema de colas Redis | 🟡 En mantenimiento (sucesor: BullMQ) | **BullMQ** (`@nestjs/bullmq`) | 🟡 Migración necesaria en el mediano plazo |
| `@nestjs/bull` v11 | Integración NestJS-Bull | 🟡 Versión atada a Bull v4 | `@nestjs/bullmq` | 🟡 Migrar junto con bull |
| `googleapis` v166 | SDK Google APIs (Gmail) | ✅ Activo, mantenido por Google | N/A | 🟢 |

---

## Dependencias de desarrollo con riesgo

| Paquete | Propósito | Estado | Riesgo |
|---|---|---|---|
| `typescript-transform-paths` | Resolución de path aliases en build | ⚠️ Paquete de comunidad, mantenimiento incierto | 🟡 |
| `husky` v9 | Git hooks | ✅ Activo | 🟢 |
| `lint-staged` v16 | Lint en staged | ✅ Activo | 🟢 |

---

## Sin dependencias "legadas" (EOL)

> [!info] Stack relativamente moderno
> A diferencia de sistemas más antiguos, este microservicio **no tiene dependencias con EOL declarado** al momento de esta revisión. El mayor riesgo es la transición de Bull v4 → BullMQ.

---

## Impacto de la migración Bull → BullMQ

Si se decide migrar:

1. Reemplazar `bull` por `bullmq` en `package.json`
2. Reemplazar `@nestjs/bull` por `@nestjs/bullmq`
3. Actualizar `CoreModule`: cambiar `BullModule.forRoot()` y `BullModule.registerQueue()`
4. Actualizar `QueueService`: cambiar `@InjectQueue` a la versión BullMQ
5. Coordinarse con el **worker externo** que consume los jobs (cambio de API en el consumer)

> [!warning] Impacto en worker externo
> El worker que consume la cola `email` también deberá migrarse a BullMQ en el mismo ciclo. Si no se coordinan, los jobs quedarán sin procesar. Ver [[deuda-tecnica]].

---

## Ver también

- [[stack-tecnologico]]
- [[deuda-tecnica]]
- [[recomendaciones-modernizacion]]
