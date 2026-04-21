# Core vs. Dependencias Custom — `muvin-ms-logs`

> **Última revisión:** 2026-04-21
> **Propósito:** Separar dependencias de framework/vendor de las customizaciones propias del equipo.

---

## Dependencias de Framework / Vendor (Core)

Estas dependencias son **estándar de la industria** y tienen soporte activo del vendor.

| Dependencia | Versión | Propósito | ¿Alternativa moderna disponible? | Riesgo de lock-in |
|-------------|---------|-----------|----------------------------------|-------------------|
| `@nestjs/common` | ^11.0.1 | Decoradores, pipes, guards | NestJS es el estándar — no requiere cambio | 🟢 Bajo |
| `@nestjs/core` | ^11.0.1 | Bootstrap del framework | — | 🟢 Bajo |
| `@nestjs/microservices` | ^11.1.6 | Transporte TCP | Reemplazable por gRPC, NATS, RabbitMQ si crece | 🟡 Medio |
| `@nestjs/platform-express` | ^11.0.1 | Plataforma HTTP (no usada) | — (⚠️ innecesaria en este MS) | 🟡 Peso muerto |
| `@prisma/client` + `prisma` | ^6.16.1 | ORM + migraciones | Alternativas: Drizzle, TypeORM, Kysely | 🟢 Bajo |
| `rxjs` | ^7.8.1 | Programación reactiva (interna NestJS) | Built-in a NestJS — no reemplazable sin cambiar framework | 🟢 Bajo |
| `class-validator` | ^0.14.2 | Validación de DTOs | Zod (más moderno), Joi | 🟢 Bajo |
| `class-transformer` | ^0.5.1 | Serialización | — | 🟢 Bajo |
| `joi` | ^18.0.1 | Validación de env vars | Zod (más moderno) | 🟢 Bajo |
| `dotenv` | ^17.2.2 | Carga de `.env` | `@nestjs/config` (wrapper oficial) | 🟢 Bajo |
| `reflect-metadata` | ^0.2.2 | Decoradores (metadatos) | Required por NestJS | 🟢 Bajo |
| `zlib` (Node built-in) | Built-in | Brotli compression | — | 🟢 Bajo |
| `MySQL 8.0` (Docker) | 8.0 | Base de datos | PostgreSQL como alternativa más rica en features | 🟡 Medio |

---

## Customizaciones Propias del Equipo Muvin

Estas son implementaciones **escritas por el equipo**, sin dependencia de vendor.

| Módulo / Función | Ubicación | Propósito | ¿Puede extraerse como librería? | Complejidad |
|-----------------|-----------|-----------|-------------------------------|-------------|
| `LOG()` — Logger coloreado | `src/common/functions/logger.ts` | Wrapper de NestJS Logger con colores ANSI | Sí — es un wrapper simple | Baja |
| `compressJsonFn` / `decompressJsonFn` | `src/core/utils/json.ts` | Serialización + compresión Brotli Q11 | Sí — genérico | Baja |
| `extractSearchableFieldsFn` | `src/core/utils/terms.ts` | Extrae campos de negocio (cupo, CTG, carta porte, etc.) por aliases | Parcialmente — los aliases son dominio-específico | Media |
| `mergeSearchTermsFn` | `src/core/utils/terms.ts` | Combina términos de búsqueda existentes con nuevos | No — muy atado al dominio | Baja |
| `CMDS` — Message patterns | `src/common/cmd/constant.ts` | Define las rutas TCP del MS | No — son strings de routing | Baja |
| `environments` — Env validation | `src/config/environments.ts` | Validación Joi tipada de env vars | Sí — patrón reutilizable | Baja |
| `TContractMsLogs` | `src/contracts/ms-logs/` | Contrato de tipos público del MS | Sí — debería vivir en un monorepo shared | Media |
| `IDENTITY` | `src/common/functions/identity.ts` | Función identidad `x => x` | Inaplicable — es un one-liner | Mínima |

---

## Dependencias sin alternativa moderna (bloqueantes)

En este MS **no se detectaron** dependencias bloqueadas o sin alternativa moderna. Todas las dependencias tienen soporte activo y migración posible.

---

## Dependencias candidatas a reemplazar (no urgente)

| Dependencia actual | Reemplazo sugerido | Beneficio | Urgencia |
|-------------------|-------------------|-----------|---------|
| `joi` (env validation) | `@nestjs/config` + Zod | Integración nativa con NestJS | 🟢 Baja |
| `class-validator` + `class-transformer` | Zod con `nestjs-zod` | Un solo sistema de validación | 🟢 Baja |
| `dotenv` standalone | `@nestjs/config` | Gestión de config más robusta | 🟢 Baja |
| `console.log` para errores en catch | NestJS `Logger` o `Pino` | Logs estructurados, nivel de log controlable | 🟡 Media |

---

*Ver también: [[stack-tecnologico]] · [[deuda-tecnica]] · [[recomendaciones-modernizacion]]*
