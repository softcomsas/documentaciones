---
tags: [inventario, archivos de datos, configuración]
última-revisión: 2026-04-27
---

# Índice de archivos de datos y configuración

## Variables de entorno

| Archivo | Formato | Propósito | ¿Versionado? | Notas |
|---------|---------|-----------|:------------:|-------|
| `.env-template` | Texto plano | Plantilla de variables requeridas | ✅ Sí | Sin valores reales. Referencia para desarrolladores. |
| `.env` | Texto plano | Variables de entorno reales | ❌ No | En `.gitignore`. Crear localmente a partir del template. |

**Variables requeridas:**

| Variable | Tipo | Ejemplo | Obligatoria |
|----------|------|---------|:-----------:|
| `HOST` | string | `localhost` | ✅ |
| `PORT` | number | `4002` | ✅ |
| `DATABASE_URL` | string | `mysql://user:pass@host:3306/db` | ✅ |

## Base de datos

| Archivo | Formato | Propósito | ¿Se regenera? | Notas |
|---------|---------|-----------|:-------------:|-------|
| `prisma/schema.prisma` | Prisma DSL | Definición del esquema MySQL | Manual | ⚠️ Sin modelos definidos actualmente |
| `prisma/generated/client/` | TS/JS generado | Cliente Prisma compilado | `npm run prisma:generate` | No versionado. Se regenera en CI antes del build. |

## Docker

| Archivo | Formato | Propósito | ¿Versionado? |
|---------|---------|-----------|:------------:|
| `docker/Dockerfile` | Dockerfile | Imagen de producción multi-stage (node:20-alpine) | ✅ |
| `docker/docker-compose.yml` | YAML | MySQL 8.0 local para desarrollo | ✅ |

## CI/CD

| Archivo | Formato | Propósito | Trigger |
|---------|---------|-----------|---------|
| `.github/workflows/deploy-dev.yml` | YAML | Build Docker + deploy SSH a servidor DEV | Push a rama `dev` |
| `.github/workflows/sync-cap.yml` | YAML | Sincronización con repositorio externo | Push/delete en rama `cap` |

## Herramientas de desarrollo

| Archivo | Propósito |
|---------|-----------|
| `tsconfig.json` | Configuración TypeScript base |
| `tsconfig.build.json` | Configuración TypeScript para build de producción (excluye `node_modules`, `dist`) |
| `tsconfig.paths.json` | Path aliases (`@common`, `@config`, `@core`, `@contracts`, `@db`) |
| `nest-cli.json` | Configuración del CLI de NestJS (entry file, source root) |
| `eslint.config.mjs` | Reglas de linting (ESLint flat config) |
| `.prettierrc` | Reglas de formateo (Prettier) |
| `.eslintignore` | Archivos excluidos de linting |
| `.prettierignore` | Archivos excluidos de formateo |
| `.husky/pre-commit` | Hook pre-commit: ejecuta lint-staged |
| `package.json` | Dependencias, scripts NPM, metadata del proyecto |
| `package-lock.json` | Lockfile de dependencias (reproducibilidad) |
