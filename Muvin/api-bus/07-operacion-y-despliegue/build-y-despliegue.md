# Operación y Despliegue — api-bus

> **Última revisión:** 2026-04-29

---

## Requisitos de entorno

| Requisito | Versión mínima | Notas |
|-----------|---------------|-------|
| PHP | ≥ 5.6 | ⚠️ EOL desde dic 2018. Recomendado migrar a PHP 8.x |
| Yii 2 | ~2.0.14 | Framework principal |
| Composer | ≥ 1.x | Gestión de dependencias PHP |
| Apache | 2.4+ | Servidor web (mod_rewrite requerido) |
| MySQL | 5.7+ / 8.x | Base de datos (esquema Viterra + tabla SanMiguelKey) |
| Docker | 20.x+ | Para despliegue containerizado |

---

## Variables de entorno y configuración

La configuración principal está en `source/config/main.php` y `source/config/params.php`.

> ⚠️ **CRÍTICO:** Actualmente las credenciales están hardcodeadas en `main.php`. En un entorno seguro deben migrarse a variables de entorno o un gestor de secretos.

### Parámetros a configurar

| Parámetro | Ubicación | Descripción |
|-----------|-----------|-------------|
| `passOpenSSL` (silohub) | `main.php` | Clave AES para descifrar params SiloHub |
| `passOpenSSL` (timbues) | `main.php` | Clave AES para descifrar params Timbúes |
| `client_id` / `client_secret` | `main.php` | Credenciales OAuth2 SiloHub |
| `urlBaseSiloHub` | `main.php` | URL base de la API SiloHub |
| `empresaSiloHub` | `main.php` | Código empresa en SiloHub (verificar si es `PRU`) |
| `urlBaseTimbues` | `main.php` | URL base de Timbúes |
| `urlCerealTrack` | `params.php` | URL CerealTrack / Bayer GPS |
| `urlBaseZarcam` | `main.php` | URL base de Zarcam |
| `usernameZarcam` / `secretZarcam` | `main.php` | Credenciales Zarcam |
| `urlBaseSanMiguel` | `main.php` | URL GPS SanMiguel (verificar si `desa3` es prod) |
| `db.dsn` | `main.php` | Conexión a base de datos MySQL |
| `db.username` / `db.password` | `main.php` | Credenciales DB |
| `jwtSecretKey` | `params.php` | Clave para validar JWT entrantes |

---

## Despliegue con Docker

### Estructura Docker

```
api-bus/
├── Dockerfile              # Build imagen
├── docker-compose.yaml     # Orquestación
└── config/
    └── 000-default.conf    # VirtualHost Apache
```

### Build y run

```bash
# Build
docker build -t api-bus .

# Run con docker-compose
docker-compose up -d
```

### Dockerfile (resumen)

```dockerfile
FROM php:5.6-apache          # ⚠️ PHP 5.6 EOL
RUN docker-php-ext-install pdo pdo_mysql
COPY source/ /var/www/html/
COPY config/000-default.conf /etc/apache2/sites-available/
RUN a2enmod rewrite
```

> ⚠️ La imagen base es PHP 5.6 (EOL). Se recomienda migrar a `php:8.2-apache`.

---

## Inicialización

```bash
# Inicializar entorno (dev o prod)
cd source/
php init --env=Development --overwrite=All   # desarrollo
php init --env=Production --overwrite=All    # producción

# Instalar dependencias
composer install --no-dev    # producción
composer install             # desarrollo

# Correr migraciones
php yii migrate
```

---

## Estructura de entornos

El comando `init` copia los archivos de `source/environments/dev/` o `source/environments/prod/` a sus destinos. Esto incluye `config/main-local.php` con valores sensibles que **no deben commitearse**.

```
environments/
├── dev/
│   └── config/
│       └── main-local.php   # Configuración local dev
└── prod/
    └── config/
        └── main-local.php   # Configuración local prod
```

---

## URLs de acceso

| Entorno | URL |
|---------|-----|
| Local | `http://localhost/api/` |
| Docker | `http://localhost:{puerto}/api/` |
| Producción | Configurado en VirtualHost Apache |

---

## Logs

- **Logs Yii:** `source/runtime/logs/app.log`
- **Logs Apache:** `/var/log/apache2/` (en Docker)
- **Nivel de log:** Configurable en `main.php` → componente `log`

---

## Monitoreo

> ℹ️ No se detectó configuración de monitoreo activo (APM, healthcheck endpoint, alertas). Se recomienda agregar un endpoint `/health` y conectar con sistema de monitoreo.

---

## Referencias

- [[arquitectura-alto-nivel]]
- [[stack-tecnologico]]
- [[deuda-tecnica]]
