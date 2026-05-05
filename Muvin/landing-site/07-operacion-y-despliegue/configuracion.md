# Configuración — Landing Site Muvin

## Variables de entorno (`.env`)

| Variable | Descripción | Usada por |
|----------|-------------|-----------|
| `MYSQL_ROOT_PASSWORD` | Contraseña root de MySQL | Servicio `db` |
| `MYSQL_USER` | Usuario de aplicación MySQL | Servicios `db` y `wordpress` |
| `MYSQL_PASSWORD` | Contraseña del usuario de aplicación | Servicios `db` y `wordpress` |

## Variables hardcodeadas en `docker-compose.yml`

| Variable | Valor | Servicio |
|----------|-------|---------|
| `MYSQL_DATABASE` | `wordpress` | `db` |
| `WORDPRESS_DB_HOST` | `db:3306` | `wordpress` |
| `WORDPRESS_DB_NAME` | `wordpress` | `wordpress` |
| `SERVER_NAME` | `muvinapp.com` | `wordpress`, `webserver` |

## Configuración Nginx relevante

| Parámetro | Valor | Archivo |
|-----------|-------|---------|
| Puerto de escucha interno | `8081` | `nginx.conf` |
| Puerto expuesto al host | `8088` | `docker-compose.yml` |
| Server name | `muvinapp.com www.muvinapp.com` | `nginx.conf` |
| Root del documento | `/var/www/html` | `nginx.conf` |
| FastCGI backend | `wordpress:9000` | `nginx.conf` |
| `server_tokens` | `off` | `nginx.conf` |
| `autoindex` | `off` | `nginx.conf` (⚠️ ausente en `nginx.conf.new`) |

## Configuración de seguridad Nginx

Ver [[security-inventory]] y [[nginx-seguridad]] para el detalle completo de reglas de acceso y headers.

## Whitelist de IPs (`allowip.ip`)

Archivo incluido en `nginx.conf` para ampliar las IPs con acceso a `wp-login.php`. El contenido exacto no es visible en el repositorio público pero está versionado.

```nginx
include ./conf.d/allowip.ip;
```
