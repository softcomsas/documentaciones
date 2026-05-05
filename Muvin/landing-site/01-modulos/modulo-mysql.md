# Módulo: MySQL Database

> **Imagen Docker:** `mysql:5.7`
> **Hostname interno:** `db-muvinapp-prd`
> **Puerto interno:** `3306`
> **Criticidad:** 🔴 Alta
> **Estado:** Producción (versión EOL)

## Propósito

Provee la capa de persistencia de datos para WordPress. Almacena todo el contenido del sitio: páginas, posts, configuración de WordPress, usuarios administradores, opciones de plugins y temas. No está expuesto fuera de la red Docker interna (`app-network`).

## Dependencias

- **Depende de:** ninguno
- **Es usado por:** [[modulo-wordpress]] (conexión `db:3306`)
- **Consume servicios backend:** ninguno

## Configuración

| Parámetro | Valor |
|-----------|-------|
| Base de datos creada | `wordpress` |
| Plugin de autenticación | `mysql_native_password` |
| Puerto expuesto al host | ❌ No expuesto (solo red interna) |
| Volumen de datos | `/opt/landingpage/dbdata:/var/lib/mysql` |

## Variables de entorno requeridas

| Variable | Fuente | Descripción |
|----------|--------|-------------|
| `MYSQL_ROOT_PASSWORD` | `.env` | Contraseña root de MySQL |
| `MYSQL_USER` | `.env` | Usuario de aplicación |
| `MYSQL_PASSWORD` | `.env` | Contraseña del usuario de aplicación |
| `MYSQL_DATABASE` | `docker-compose.yml` | `wordpress` (fijado en compose) |

## Riesgos y deuda técnica

- 🔴 **MySQL 5.7 EOL** — llegó a fin de vida en octubre 2023. Sin parches de seguridad oficiales.
- 🔴 **`mysql_native_password`** — plugin de autenticación legacy, deshabilitado por defecto en MySQL 8.x. Indica dependencia de comportamiento antiguo.
- ⚠️ **Backup de datos no automatizado en la configuración versionada** — solo existe el plugin de WordPress para backups. No hay dump SQL periódico definido en el repositorio.
- ⚠️ **Credenciales en `.env` sin cifrar** — el archivo `.env` contiene credenciales en texto plano. Debe verificarse que no esté versionado en el repositorio.
- 🔴 **No hay réplica ni alta disponibilidad** — setup single-node. Si el contenedor cae, el sitio queda offline.

## Archivos fuente relevantes

- `docker-compose.yml` — definición del servicio `db`
- `.env` — credenciales (⚠️ no debe estar en control de versiones)
