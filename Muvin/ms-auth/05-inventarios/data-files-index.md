# Inventario: Archivos de Datos y Configuración

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## Archivos de configuración de entorno

| Archivo | Propósito | Formato | ¿Se regenera? | Riesgo |
|---------|-----------|---------|--------------|--------|
| `.env-template` | Plantilla de variables de entorno requeridas | Texto plano (KEY=valor) | Manual — referencia para crear `.env` real | 🟢 Seguro — sin valores reales |
| `.env` | Variables de entorno reales en ejecución | Texto plano | Manual por cada entorno | 🔴 No commitear — contiene credenciales |

**Variables requeridas declaradas en `.env-template`:**

| Variable | Descripción | Ejemplo |
|----------|-------------|---------|
| `HOST` | Dirección donde escucha el microservicio TCP | `localhost` |
| `PORT` | Puerto TCP del microservicio | `4001` |
| `DATABASE_URL` | Connection string de MySQL | `mysql://user:pass@host:port/db` |

---

## Esquema de base de datos

| Archivo | Propósito | Formato | ¿Se regenera? | Riesgo |
|---------|-----------|---------|--------------|--------|
| `prisma/schema.prisma` | Definición del esquema de BD y modelos Prisma | Prisma SDL | Manual — fuente de verdad del modelo | 🟡 Cambios sin migración pueden romper producción |
| `prisma/generated/client/` | Cliente Prisma generado automáticamente | TypeScript / JS | Automático — `npm run prisma:generate` | ⚠️ No editar manualmente |

> [!warning] Migraciones ausentes
> No se detectó la carpeta `prisma/migrations/`. Esto significa que o bien las migraciones no están versionadas en este repositorio, o el esquema se aplica directamente con `prisma db push`. Esto es un riesgo operativo en producción. Ver [[deuda-tecnica]].

---

## Archivos de configuración del proyecto

| Archivo | Propósito | Formato | ¿Se regenera? |
|---------|-----------|---------|--------------|
| `package.json` | Dependencias, scripts y metadatos del proyecto Node | JSON | Manual |
| `package-lock.json` | Lockfile de dependencias (npm) | JSON | Automático — `npm install` |
| `tsconfig.json` | Configuración base de TypeScript | JSON | Manual |
| `tsconfig.build.json` | Config de TypeScript para compilación de producción | JSON | Manual |
| `tsconfig.paths.json` | Path aliases (`@common`, `@config`, etc.) | JSON | Manual |
| `nest-cli.json` | Configuración del CLI de NestJS | JSON | Manual |
| `.prettierrc` | Reglas de formateo de código | JSON | Manual |
| `eslint.config.mjs` | Reglas de linting (flat config ESLint 9+) | ESM JS | Manual |

---

## Archivos de infraestructura

| Archivo | Propósito | Formato | ¿Se regenera? |
|---------|-----------|---------|--------------|
| `docker/Dockerfile` | Imagen Docker multi-stage para producción | Dockerfile | Manual |
| `docker/docker-compose.yml` | Orquestación local: MySQL 8.0 + ms-auth | YAML | Manual |
| `.dockerignore` | Exclusiones del contexto de build Docker | Texto plano | Manual |

---

## Archivos de CI/CD

| Archivo | Propósito | Formato | ¿Se regenera? |
|---------|-----------|---------|--------------|
| `.github/workflows/deploy-dev.yml` | Pipeline de deploy automático a DEV | YAML (GitHub Actions) | Manual |
| `.github/workflows/sync-cap.yml` | Sincronización con sistema CAP | YAML (GitHub Actions) | Manual — ⚠️ propósito pendiente de verificar |

---

## Archivos de calidad de código

| Archivo | Propósito | Formato |
|---------|-----------|---------|
| `.husky/pre-commit` | Hook git pre-commit: ejecuta lint-staged | Shell |
| `.eslintignore` | Exclusiones del linter ESLint | Texto plano |
| `.prettierignore` | Exclusiones del formateador Prettier | Texto plano |
| `.gitignore` | Exclusiones del repositorio git | Texto plano |

---

## Archivos de salida compilada

| Archivo / Carpeta | Propósito | ¿Commitear? |
|---|---|---|
| `dist/` | Salida compilada de TypeScript para producción | ❌ No — generado por `npm run build` |
| `prisma/generated/` | Cliente Prisma generado | ❌ No — generado por `prisma generate` |
| `node_modules/` | Dependencias instaladas | ❌ No |

---

## Archivos binarios o embebidos

> [!info] Sin archivos binarios detectados
> No se detectaron plantillas de reportes, seeds de base de datos, fixtures, imágenes embebidas ni archivos binarios en el repositorio.

---

## Resumen por categoría

| Categoría | Cantidad de archivos |
|-----------|---------------------|
| Configuración de entorno | 2 |
| Esquema de BD | 2 (+generados) |
| Configuración del proyecto | 8 |
| Infraestructura (Docker) | 3 |
| CI/CD | 2 |
| Calidad de código | 4 |
| **Total** | **21** |
