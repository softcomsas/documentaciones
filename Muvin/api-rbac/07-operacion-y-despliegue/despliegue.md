# Operación y Despliegue

> **Stack:** PHP >=7.2 · Yii2 · MySQL · Docker

## Requisitos

| Herramienta | Versión mínima |
|-------------|----------------|
| Docker | 20.x |
| Docker Compose | 2.x |
| PHP (sin Docker) | 7.2+ |
| Composer | 2.x |

## Despliegue con Docker

```powershell
# 1. Copiar variables de entorno
Copy-Item .example.env .env
# Editar .env con valores reales

# 2. Levantar servicios
docker-compose up -d

# Servicios disponibles:
# - API:        http://localhost:8080
# - phpMyAdmin: http://localhost:8081
# - Swagger UI: http://localhost:8080/
```

## Variables de entorno requeridas

| Variable | Descripción | ⚠️ Cambiar en producción |
|----------|-------------|--------------------------|
| `MYSQL_ROOT_PASSWORD` | Root MySQL | ✅ Sí |
| `MYSQL_DATABASE` | Nombre de BD | — |
| `MYSQL_USER` | Usuario MySQL | ✅ Sí |
| `MYSQL_PASSWORD` | Contraseña MySQL | ✅ Sí |
| `RBAC_ENV` | `Development` o `Production` | ✅ Sí |
| `RBAC_PORT` | Puerto del servicio (default 8080) | — |

## Configuración de aplicación

Editar `source/config/params.php` o usar `main-local.php`:

```php
// Parámetros críticos a cambiar
'jwt_secret' => getenv('JWT_SECRET') ?: 'CAMBIAR_EN_PRODUCCION',
'jwt_expiration' => 3600,
```

El `seed` de `StrongTokenAuth` está en `source/config/main.php`:

```php
'as accessControl' => [
    'class' => StrongTokenAuth::class,
    'seed' => getenv('RBAC_SEED') ?: 'CAMBIAR_EN_PRODUCCION',
]
```

> 🔴 Ambos valores deben ser strings aleatorios de alta entropía en producción.

## Migraciones

```bash
# Dentro del container
docker exec -it rbac_app php yii migrate

# O con PHP local
cd source
php yii migrate
php yii migrate/up          # Aplicar pendientes
php yii migrate/down 1      # Revertir última
php yii migrate/history     # Ver historial
```

## Inicialización del entorno Yii2

```bash
# Dev
php init --env=Development

# Producción
php init --env=Production
```

Esto copia los archivos de `environments/dev/` o `environments/prod/` al lugar correcto.

## CI/CD

El archivo `.gitlab-ci.yml` define el pipeline de despliegue en GitLab CI. Revisar el archivo para los stages configurados.

## Swagger UI

Disponible en `http://{host}/` (ruta `default/index`). No requiere autenticación.

La spec OpenAPI se genera automáticamente desde las anotaciones `@OA\*` en controllers y models.
