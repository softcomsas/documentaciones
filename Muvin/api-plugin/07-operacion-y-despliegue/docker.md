# Docker — Containerización

**Archivos:**
- `api-plugin-main/Dockerfile`
- `api-plugin-main/docker-compose.yaml`
- `source/docker-compose.yml` (alternativo para desarrollo local)

## Dockerfile

El Dockerfile construye la imagen del plugin:

```dockerfile
# Imagen base PHP con extensiones necesarias (imap, mbstring, openssl, pdo, curl, dom)
FROM php:8.x-cli

# Instalar extensiones PHP
RUN docker-php-ext-install pdo pdo_mysql mbstring

# Instalar php-imap (requiere instalación específica)
RUN apt-get install -y libc-client-dev libkrb5-dev \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap

# Instalar Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copiar código fuente
COPY source/ /app/

# Instalar dependencias
RUN cd /app && composer install --no-dev

# Copiar y configurar cron
COPY cron/plugin_cupos /etc/cron.d/plugin_cupos
RUN chmod 0644 /etc/cron.d/plugin_cupos && crontab /etc/cron.d/plugin_cupos

# Directorio de trabajo
WORKDIR /app

# Iniciar cron en foreground
CMD ["cron", "-f"]
```

> ⚠️ El Dockerfile real puede diferir. Revisar `api-plugin-main/Dockerfile` para la versión exacta.

## docker-compose.yaml

```yaml
version: '3'
services:
  plugin:
    build: .
    environment:
      - DB_HOST=db
      - DB_NAME=plugin_db
      - DB_USER=plugin_user
      - DB_PASS=plugin_pass
    depends_on:
      - db
    volumes:
      - ./source:/app   # para desarrollo (hot-reload)
      - plugin_logs:/var/log

  db:
    image: mysql:5.7
    environment:
      - MYSQL_DATABASE=plugin_db
      - MYSQL_USER=plugin_user
      - MYSQL_PASSWORD=plugin_pass
      - MYSQL_ROOT_PASSWORD=root_pass
    volumes:
      - plugin_db:/var/lib/mysql

volumes:
  plugin_db:
  plugin_logs:
```

> ⚠️ Revisar `docker-compose.yaml` real para la configuración exacta.

## Comandos de operación

```bash
# Construir la imagen
docker-compose build

# Levantar el servicio
docker-compose up -d

# Ver logs
docker-compose logs -f plugin

# Ejecutar un proceso manualmente
docker-compose exec plugin php yii parametros
docker-compose exec plugin php yii lector

# Ver logs del cron
docker-compose exec plugin tail -f /var/log/cron.log

# Reiniciar el servicio
docker-compose restart plugin

# Detener
docker-compose down
```

## CI/CD — GitLab CI

El archivo `.gitlab-ci.yml` define el pipeline de integración y despliegue continuo. Típicamente incluye:
- Build de la imagen Docker
- Push al registro de imágenes
- Deploy al entorno de producción

## Variables de entorno necesarias

| Variable | Descripción |
|---|---|
| `DB_HOST` | Host de la base de datos MySQL |
| `DB_PORT` | Puerto MySQL (default: 3306) |
| `DB_NAME` | Nombre de la base de datos |
| `DB_USER` | Usuario MySQL |
| `DB_PASS` | Password MySQL |

> ⚠️ Las credenciales de Muvin API NO se toman de variables de entorno actualmente — están hardcodeadas en `ServiciosMuvin.php`.
