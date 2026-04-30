# Stack Tecnológico — api-wscpe

> [[README]]

## Core

| Componente | Versión | Rol |
|-----------|---------|-----|
| PHP | `>=7.4.0` | Lenguaje de runtime |
| Yii2 Basic | `~2.0.45` | Framework MVC/REST |
| MySQL | no especificado (Yii2 ActiveRecord) | Base de datos |

## Dependencias de producción (`composer.json`)

| Paquete | Versión | Uso |
|---------|---------|-----|
| `yiisoft/yii2` | `~2.0.45` | Framework base |
| `yiisoft/yii2-bootstrap5` | `~2.0.2` | UI (Swagger page) |
| `yiisoft/yii2-symfonymailer` | `~2.0.3` | Mailer (no usado activamente) |
| `light/yii2-swagger` | `~1.0.4` | Documentación Swagger UI / Swagger 2.0 |

## Dependencias de desarrollo

| Paquete | Versión | Uso |
|---------|---------|-----|
| `yiisoft/yii2-debug` | `~2.1.0` | Debug toolbar |
| `yiisoft/yii2-gii` | `~2.2.0` | Code generator |
| `phpunit/phpunit` | `~9.5.0` | Tests unitarios |
| `codeception/codeception` | `^5.0.0 || ^4.0` | Tests de aceptación/funcionales |

## Componentes PHP built-in utilizados

| Componente | Uso |
|-----------|-----|
| `SoapClient` | Llamadas al WS SOAP de AFIP |
| `file_put_contents` | Escritura de certificados en disco (`Afip_res/`) |
| `openssl` (extensión) | Procesamiento de certificados X.509 |
| `json_encode` / `json_decode` | Serialización de cache y respuestas |

## Autenticación

El token de acceso es un **MD5 de `uniqid()`** con expiración de **9 horas**:
```php
$user->token = md5(uniqid());
$user->token_expire = time() + (60 * 60 * 9);
```

> 🔴 MD5 no es adecuado para tokens de seguridad. Ver [security-inventory](../05-inventarios/security-inventory.md).

## Notas de compatibilidad

- Yii2 está en modo mantenimiento. No hay versión 3 planificada con el mismo API.
- PHP 7.4 alcanzó EOL en noviembre 2022. Mínimo recomendado: PHP 8.1+.
- `SoapClient` requiere la extensión `php-soap` habilitada en el servidor.
