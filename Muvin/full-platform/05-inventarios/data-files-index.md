# Índice de Archivos de Datos

> **Última revisión:** 2026-04-29

## Scripts de Migración

| Archivo | Módulo | Propósito | Formato | Regenerable | Notas |
|---|---|---|---|---|---|
| `migrations/20260212175127-insert-motivos-error-adicionales.js` | Global (raíz monorepo) | Inserta motivos de error adicionales en la DB | JS (Sequelize migration) | No — manual | Migración datada Feb 2026 |
| `descargas-app/backend/migrations/` | descargas-app | Migraciones de base de datos para el backend de descargas | ⚠️ Pendiente de listar archivos individuales | No — manual | Usar `sequelize-cli` |
| `logistica-app/backend/api/source/migrations/` | logistica-app | Migraciones Yii2 PHP (ActiveRecord migrations) | PHP | No — manual | Ejecutar con `yii migrate` |
| `oferta-app/backend/api/source/migrations/` | oferta-app | Migraciones Yii2 PHP | PHP | No — manual | Ídem |
| `documentacion-app/backend/api/source/migrations/` | documentacion-app | Migraciones Yii2 PHP | PHP | No — manual | Incluye `console/` para comandos de migración |

## Archivos de Configuración

| Archivo | Módulo | Propósito | Formato | Regenerable |
|---|---|---|---|---|
| `descargas-app/backend/.env-template` | descargas-app | Template de variables de entorno para el backend NestJS | ENV template | Sí (template) |
| `descargas-app/backend/config/` | descargas-app | Configuración Sequelize (DB connection) | TS | Sí (según ENV) |
| `logistica-app/backend/api/source/local.env` | logistica-app | Variables de entorno locales Yii2 | ENV | 🔴 No — riesgo de estar commiteado |
| `logistica-app/backend/api/source/config/` | logistica-app | Config Yii2 (db, web, console) | PHP | Parcialmente |
| `oferta-app/backend/api/source/local.env` | oferta-app | Ídem | ENV | 🔴 Ídem |
| `documentacion-app/backend/api/source/local.env` | documentacion-app | Ídem | ENV | 🔴 Ídem |
| `environments/environment.ts` | Frontend global | Variables de entorno Angular (desarrollo) | TS | Sí (build) |
| `environments/environment.prod.ts` | Frontend global | Variables de entorno Angular (producción) | TS | Sí (build) |
| `nx.json` | Monorepo | Configuración Nx (tareas, cache, plugins) | JSON | Sí (mantenido) |
| `tsconfig.base.json` | Monorepo | TypeScript paths compartidos entre apps | JSON | Sí (mantenido) |
| `tailwind.config.js` | Monorepo | Tailwind CSS config raíz | JS | Sí (mantenido) |
| `proxy.conf.json` | main (shell) | Proxy de desarrollo Angular | JSON | Sí (desarrollo) |
| `.prettierrc` | Monorepo | Config de formateo Prettier | JSON | Sí |
| `.eslintrc.json` | Monorepo | Config ESLint raíz | JSON | Sí |
| `.gitlab-ci.yml` | Monorepo | Pipeline CI/CD GitLab | YAML | Manual |

## Datos Estáticos (Static Data)

| Archivo | Módulo | Propósito | Formato | Regenerable |
|---|---|---|---|---|
| `main/src/static-data/` | main (shell) | Datos estáticos de la aplicación (catálogos, listas) | TS/JSON | ⚠️ Pendiente de listar contenido |
| `descargas-app/backend/src/models/estados.data.static.ts` | descargas-app | Estados estáticos de descarga/cupo | TS | Sí (código) |
| `descargas-app/frontend/src/app/data/` | descargas-app | Servicios de datos del frontend | TS | Sí (código) |

## Archivos de Infraestructura

| Archivo | Módulo | Propósito | Formato | Regenerable |
|---|---|---|---|---|
| `dockerfiles/frontend.base.dockerfile` | Global | Dockerfile base para frontends Angular | Dockerfile | Manual |
| `dockerfiles/nginx.conf` | Global | Config Nginx para servir frontends | Nginx conf | Manual |
| `descargas-app/backend/docker/` | descargas-app | Docker config para el backend NestJS | Dockerfile | Manual |
| `descargas-app/frontend/docker/` | descargas-app | Docker config para el frontend MFE | Dockerfile | Manual |
| `documentacion-app/backend/api/docker/` | documentacion-app | Docker para Yii2 | Dockerfile | Manual |
| `documentacion-app/backend/api/k8s/` | documentacion-app | Manifests Kubernetes | YAML | Manual |
| `logistica-app/backend/api/source/docker-compose.yml` | logistica-app | Docker Compose local | YAML | Manual |

## Scripts

| Archivo | Propósito | Formato |
|---|---|---|
| `scripts/build-frontend.sh` | Build de frontends | Shell |
| `scripts/create-module.sh` | Generador de nuevo módulo | Shell |

## Archivos de Prueba / Seeds

| Archivo | Módulo | Propósito | Notas |
|---|---|---|---|
| `descargas-app/backend/test/` | descargas-app | Tests e2e NestJS | Jest |
| `logistica-app/backend/api/source/tests/` | logistica-app | Tests Codeception PHP | PHP |
| `oferta-app/backend/api/source/tests/` | oferta-app | Tests Codeception PHP | PHP |
| `documentacion-app/backend/api/source/tests/` | documentacion-app | Tests Codeception PHP | PHP |
