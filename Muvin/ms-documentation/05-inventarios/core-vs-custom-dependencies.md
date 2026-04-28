---
tags: [inventario, dependencias, npm, paquetes]
última-revisión: 2026-04-27
---

# Dependencias core vs. customizaciones

## Dependencias de producción

| Paquete | Versión | Tipo | Alternativa moderna | Riesgo |
|---------|---------|------|--------------------:|:------:|
| `@nestjs/common` | ^11.1.9 | Framework core | — (es la última versión mayor) | 🟢 |
| `@nestjs/core` | ^11.1.9 | Framework core | — | 🟢 |
| `@nestjs/microservices` | ^11.1.9 | Framework core TCP | — | 🟢 |
| `@nestjs/platform-express` | ^11.1.9 | Adaptador HTTP (no usado) | — | 🟡 |
| `@prisma/client` | ^6.19.0 | ORM cliente | — (última versión mayor) | 🟢 |
| `rxjs` | ^7.8.2 | Streams reactivos (req. por NestJS) | — | 🟢 |
| `dotenv` | ^17.2.3 | Variables de entorno | — | 🟢 |
| `joi` | ^18.0.2 | Validación de esquemas | `zod` (mejor inferencia TS) | 🟢 |
| `reflect-metadata` | ^0.2.2 | Decoradores TS / NestJS DI | — | 🟢 |

## Dependencias de desarrollo

| Paquete | Versión | Propósito | Riesgo |
|---------|---------|-----------|:------:|
| `@nestjs/cli` | ^11.0.14 | Build y generación | 🟢 |
| `@nestjs/schematics` | ^11.0.9 | Generadores de código | 🟢 |
| `prisma` | ^6.19.0 | CLI de migraciones | 🟢 |
| `typescript` | ^5.9.3 | Compilador | 🟢 |
| `eslint` | ^9.39.2 | Linting | 🟢 |
| `typescript-eslint` | ^8.50.0 | Plugin ESLint para TS | 🟢 |
| `prettier` | ^3.7.4 | Formateo | 🟢 |
| `husky` | ^9.1.7 | Git hooks | 🟢 |
| `lint-staged` | ^16.2.7 | Lint de staged files | 🟢 |
| `ts-node` | ^10.9.2 | Ejecución TS en dev | 🟢 |
| `tsconfig-paths` | ^4.2.0 | Path aliases en runtime | 🟢 |
| `typescript-transform-paths` | ^3.5.5 | Path aliases en build | 🟢 |

## Observaciones

| # | Observación | Severidad |
|---|------------|:---------:|
| 1 | `@nestjs/platform-express` está incluido como dependencia pero este MS no expone HTTP. Es una dependencia transitiva de NestJS. Evaluar si se puede excluir. | 🟡 |
| 2 | Sin dependencias EOL ni deprecadas | 🟢 |
| 3 | Stack completamente moderno — sin deuda de versiones | 🟢 |
| 4 | `joi` funciona bien pero `zod` ofrece mejor integración con TypeScript si se considera una migración futura | 🟢 |
