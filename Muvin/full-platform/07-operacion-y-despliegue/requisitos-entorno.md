# Requisitos de Entorno

> **Última revisión:** 2026-04-29

## Entorno de Desarrollo

### Requisitos de Sistema

| Requisito | Versión requerida | Notas |
|---|---|---|
| Node.js | 18.16.0 | ⚠️ EOL. Actualizar a Node 20 LTS |
| pnpm | 8.8.0 | Instalación: `npm i -g pnpm@8.8.0` |
| Nx CLI | 16.x | Se instala con `pnpm install` del workspace |
| Docker | 20+ | Para levantar backends localmente |
| PHP | ⚠️ Pendiente de verificar | Para backends Yii2 (logística, oferta, documentación) |
| Composer | 2.x | Para instalar dependencias PHP de Yii2 |

### Variables de Entorno (descargas-app backend)

> Ver plantilla en `descargas-app/backend/.env-template`

| Variable | Propósito | Ejemplo |
|---|---|---|
| `DB_HOST` | Host de la base de datos | `localhost` |
| `DB_PORT` | Puerto de la base de datos | `3306` |
| `DB_NAME` | Nombre de la base de datos | `aisa_descargas` |
| `DB_USER` | Usuario de la base de datos | `aisa_user` |
| `DB_PASS` | Contraseña de la base de datos | `***` |
| `JWT_SECRET` | Secreto para validación de JWT | `***` |
| `AFIP_API_URL` | URL de la API de AFIP | `https://...` |
| ⚠️ Otras | Pendiente de verificar en `.env-template` | — |

### Variables de Entorno (backends Yii2)

> Ver `local.env` en cada backend. ⚠️ Verificar que no estén commiteadas al repositorio.

| Variable | Propósito |
|---|---|
| `DB_DSN` | DSN de conexión a MySQL |
| `DB_USER` | Usuario DB |
| `DB_PASSWORD` | Contraseña DB |
| `APP_ENV` | Entorno (dev/prod) |

## Entorno de Producción

| Componente | Tecnología | Notas |
|---|---|---|
| Servidor web frontend | Nginx | Config en `dockerfiles/nginx.conf` |
| Container frontend | Docker | `dockerfiles/frontend.base.dockerfile` |
| Container backend NestJS | Docker | `descargas-app/backend/docker/` |
| Container backend Yii2 | Docker | `documentacion-app/backend/api/docker/` |
| Orquestación | Kubernetes (parcial) | K8s manifests en `documentacion-app/backend/api/k8s/` |
| CI/CD | GitLab CI | `.gitlab-ci.yml` en raíz del monorepo |
