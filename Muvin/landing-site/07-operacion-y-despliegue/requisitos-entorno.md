# Requisitos de Entorno — Landing Site Muvin

## Requisitos del host servidor

| Requisito | Descripción | Notas |
|-----------|-------------|-------|
| Docker Engine | Instalado y corriendo | Versión compatible con Compose v3 |
| Docker Compose | v1.x o v2.x (CLI plugin) | Sintaxis `version: '3'` |
| Directorio `/opt/landingpage/dbdata/` | Creado en el host | Persistencia MySQL |
| Directorio `/opt/landingpage/wordpress/` | Creado en el host con WP instalado o vacío | Persistencia WordPress |
| Archivo `.env` | En la raíz del proyecto | Con las 3 variables de credenciales |
| Puerto `8088` libre | En el host | Expuesto por Nginx |
| Acceso a Docker Hub | Para descargar imágenes | En primer despliegue |

## Variables de entorno requeridas (`.env`)

```env
MYSQL_ROOT_PASSWORD=<contraseña-root-segura>
MYSQL_USER=<usuario-aplicacion>
MYSQL_PASSWORD=<contraseña-aplicacion>
```

> [!danger] Seguridad
> El archivo `.env` no debe estar en control de versiones. Verificar que esté en `.gitignore`.

## Red interna requerida

Los rangos de IP en `nginx.conf` deben ser accesibles desde las redes del servidor para poder administrar el sitio:

| Rango | Uso |
|-------|-----|
| `172.16.0.0/24` | Acceso a wp-login, xmlrpc, wp-cron, backups |
| `172.26.0.0/24` | Acceso a wp-login, xmlrpc, wp-cron, backups |
| `172.25.0.0/24` | Acceso a wp-login, xmlrpc, wp-cron, backups |
| `192.168.12.0/22` | Acceso a wp-login, xmlrpc, wp-cron, backups |
| IPs en `allowip.ip` | Acceso adicional a wp-login |

## Sistema operativo

⚠️ Pendiente de verificar — no hay especificación en el repositorio. Se asume Linux (Ubuntu/CentOS).
