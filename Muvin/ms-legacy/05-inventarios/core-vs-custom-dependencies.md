# Dependencias core vs. customizaciones

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Dependencias del framework/vendor (core)

Estas son dependencias estándar del ecosistema NestJS/Node. No son customizaciones del proyecto.

| Dependencia | Versión | Propósito | Alternativa moderna | ¿Bloqueada? |
|-------------|---------|-----------|---------------------|-------------|
| `@nestjs/core` | 11.x | Framework NestJS | — (ya es moderna) | No |
| `@nestjs/common` | 11.x | Decoradores, pipes, guards | — | No |
| `@nestjs/microservices` | 11.x | Soporte TCP/mensajes | — | No |
| `@nestjs/axios` | 4.x | Cliente HTTP | `fetch` nativo Node 18+ | No |
| `@nestjs/config` | 4.x | Gestión de configuración | — | No |
| `axios` | 1.x | HTTP client | `fetch` nativo Node 18+ | No |
| `rxjs` | 7.x | Programación reactiva (requerida por NestJS) | — | No |
| `class-validator` | 0.14.x | Validación de DTOs | Zod | No |
| `class-transformer` | 0.5.x | Transformación de objetos | — | No |
| `reflect-metadata` | 0.2.x | Soporte de decoradores | — | No |

## Dependencias de validación/utilitarios

| Dependencia | Versión | Propósito | Alternativa | ¿Bloqueada? |
|-------------|---------|-----------|-------------|-------------|
| `joi` | 18.x | Validación de variables de entorno | Zod, Valibot | No |
| `dotenv` | 17.x | Carga de `.env` | Variables nativas del sistema | No |

## Customizaciones propias del proyecto

Código desarrollado específicamente para este microservicio:

| Componente | Ubicación | Propósito | ¿Reemplazable? |
|------------|-----------|-----------|----------------|
| Sistema de tipos genérico del proxy | `src/types/` | Tipado fuerte end-to-end del proxy HTTP | Sí, con simplificación |
| QUERIES_MAP (registry) | `src/api/map.ts` | Registro de queries disponibles | Sí, con DI de NestJS |
| Adapter pattern por query | `src/api/queries/` | Transformación request/response por endpoint | Sí |
| Logger con colores ANSI | `src/common/functions/logger.ts` | Wrapping de NestJS Logger | Sí, usar Logger directamente |
| `environments.ts` con Joi | `src/config/environments.ts` | Validación de entorno al arranque | Mínima refactorización |
| Contratos (`src/contracts/`) | `src/contracts/ms-legacy/` | API pública tipada para consumidores | Sí |

## Análisis de dependencias con alternativas modernas

| Dependencia actual | Alternativa | Beneficio del cambio | Esfuerzo |
|--------------------|-------------|---------------------|----------|
| `axios` + `@nestjs/axios` | `fetch` nativo (Node 18+) | Menos dependencias, nativo | Bajo |
| `joi` | `zod` | Mejor integración TypeScript, tipos inferidos | Bajo |
| `class-validator` + `class-transformer` | `zod` con `nestjs-zod` | Un único sistema de validación/transform | Medio |
| `console.log` en controller | NestJS Logger (`LOG`) | Consistencia de logging, control de niveles | Inmediato |

## Dependencias con riesgo de EOL

| Dependencia | Riesgo | Acción recomendada |
|-------------|--------|--------------------|
| `node:20-alpine` (Docker base) | 🟡 Node 20 EOL abril 2026 | Migrar a `node:22-alpine` |

> [!info] Conclusión
> El proyecto tiene un stack completamente moderno y sano. Las customizaciones propias siguen patrones reconocibles (Registry, Adapter). El mayor riesgo no es la obsolescencia del stack sino la **incomplitud funcional** (endpoint `comprador-by-cuit` sin implementar) y el **código muerto** en `@common`.
