# Stack Tecnológico — api-rbac

## Runtime

| Tecnología | Versión | Rol |
|------------|---------|-----|
| PHP | >=7.2.0 | Lenguaje servidor |
| Yii2 | ~2.0.14 | Framework MVC/REST |
| MySQL | 5.7 / 8.x | Base de datos relacional |
| Apache | 2.4 | Web server (vía Docker) |
| Docker | — | Contenedorización |

## Dependencias de producción (`composer.json`)

| Paquete | Versión | Uso |
|---------|---------|-----|
| `yiisoft/yii2` | ~2.0.14 | Core framework |
| `yiisoft/yii2-bootstrap4` | ~2.0.0 | UI (solo Swagger page) |
| `yiisoft/yii2-swiftmailer` | ~2.0.0 \|\| ~2.1.0 | Mailer (no usado activamente) |
| `light/yii2-swagger` | 3.0.2 | Documentación OpenAPI/Swagger |
| `sizeg/yii2-jwt` | ^2.0 | JWT HS512 |

## Dependencias de desarrollo

| Paquete | Versión | Uso |
|---------|---------|-----|
| `yiisoft/yii2-debug` | ~2.1.0 | Debug toolbar |
| `yiisoft/yii2-gii` | ~2.2.0 | Generador de código |
| `codeception/codeception` | ^4.0 | Tests |

## Infraestructura Docker

```yaml
# docker-compose.yaml (simplificado)
services:
  rbac:        # PHP + Apache, puerto RBAC_PORT (default 8080)
  db:          # MySQL, base de datos rbac
  phpmyadmin:  # Puerto RBAC_PHPMYADMIN_PORT (default 8081)
```

## Variables de entorno (`.example.env`)

| Variable | Valor ejemplo | Descripción |
|----------|---------------|-------------|
| `MYSQL_ROOT_PASSWORD` | `password` | Root MySQL |
| `MYSQL_DATABASE` | `rbac` | Nombre de la base |
| `MYSQL_USER` | `rbac` | Usuario MySQL |
| `MYSQL_PASSWORD` | `password` | Contraseña MySQL |
| `RBAC_ENV` | `Development` | Entorno Yii2 |
| `RBAC_PORT` | `8080` | Puerto del servicio |
| `RBAC_PHPMYADMIN_PORT` | `8081` | Puerto phpMyAdmin |

## Parámetros de aplicación (`config/params.php`)

| Parámetro | Valor por defecto | Descripción |
|-----------|-------------------|-------------|
| `jwt_secret` | `my_secret_key` | 🔴 Secret JWT — debe cambiarse |
| `jwt_expiration` | `3600` | Expiración del token en segundos |
