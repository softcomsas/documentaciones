# Módulo: Docker Compose

> **Archivo:** `docker-compose.yml`
> **Versión de sintaxis:** `3`
> **Criticidad:** 🟡 Media
> **Estado:** Producción

## Propósito

Define y orquesta los tres servicios del landing site (MySQL, WordPress, Nginx) como un stack Docker Compose. Configura la red interna, los volúmenes persistentes, las variables de entorno y las políticas de reinicio.

## Servicios definidos

| Servicio | Imagen | Hostname | Depende de |
|----------|--------|----------|------------|
| `db` | `mysql:5.7` | `db-muvinapp-prd` | — |
| `wordpress` | `wordpress:5.3.2-fpm-alpine` | `wp-muvinapp-prd` | `db` |
| `webserver` | `nginx:1.15.12-alpine` | `web-muvinapp-prd` | `wordpress` |

## Diagrama de dependencias de arranque

```mermaid
graph LR
    DB[db\nMySQL 5.7] --> WP[wordpress\nWP 5.3.2-fpm]
    WP --> NGX[webserver\nNginx 1.15.12]
```

## Volúmenes configurados

| Servicio | Ruta host | Ruta contenedor | Propósito |
|----------|-----------|-----------------|-----------|
| `db` | `/opt/landingpage/dbdata` | `/var/lib/mysql` | Datos MySQL persistentes |
| `wordpress` | `/opt/landingpage/wordpress` | `/var/www/html` | Archivos WP (código + uploads) |
| `webserver` | `/opt/landingpage/wordpress` | `/var/www/html` | Compartido con WP para servir estáticos |
| `webserver` | `./nginx-conf` | `/etc/nginx/conf.d` | Configuración Nginx |

## Red

| Red | Driver | Descripción |
|-----|--------|-------------|
| `app-network` | `bridge` | Red interna que conecta los tres servicios |

## Política de reinicio

Todos los servicios tienen `restart: unless-stopped` — se reinician automáticamente salvo que se detengan manualmente.

## Dependencias

- **Depende de:** [[modulo-mysql]], [[modulo-wordpress]], [[modulo-nginx]]
- **Requiere en el host:** archivo `.env` con `MYSQL_ROOT_PASSWORD`, `MYSQL_USER`, `MYSQL_PASSWORD`

## Riesgos y deuda técnica

- ⚠️ **Sintaxis `version: '3'`** — la clave `version` está deprecada en Docker Compose v2+. Sin impacto funcional inmediato pero señal de antigüedad.
- ⚠️ **`depends_on` sin healthcheck** — Docker Compose espera que el contenedor `db` esté iniciado pero no que MySQL esté listo para recibir conexiones. WordPress puede fallar en el arranque si MySQL tarda en inicializarse.
- 🔴 **Credenciales en `.env` no cifradas** — deben manejarse con secrets management en entornos productivos reales.
- ⚠️ **Volúmenes en rutas absolutas del host** — `/opt/landingpage/` debe existir previamente en el servidor. No hay documentación de setup del host.

## Archivos fuente relevantes

- `docker-compose.yml`
- `.env` (⚠️ no versionado — ver [[data-files-index]])
