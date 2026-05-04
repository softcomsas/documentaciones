# Inventario de Dependencias

> **PHP:** >=7.2.0 | **Gestor:** Composer

## Producción

| Paquete | Versión | Propósito | Estado |
|---------|---------|-----------|--------|
| `yiisoft/yii2` | ~2.0.14 | Framework core | ⚠️ Versión 2.0.x — no EOL pero 2.x ya no recibe features |
| `yiisoft/yii2-bootstrap4` | ~2.0.0 | UI Bootstrap4 (solo Swagger page) | ⚠️ Bootstrap 4 — obsoleto vs Bootstrap 5 |
| `yiisoft/yii2-swiftmailer` | ~2.0 \|\| ~2.1 | Mailer | ⚠️ SwiftMailer deprecado — migrar a Symfony Mailer |
| `light/yii2-swagger` | 3.0.2 | OpenAPI / Swagger UI | ✅ Funcional |
| `sizeg/yii2-jwt` | ^2.0 | JWT HS512 | ⚠️ API antigua de lcobucci/jwt |

## Desarrollo

| Paquete | Versión | Propósito |
|---------|---------|-----------|
| `yiisoft/yii2-debug` | ~2.1.0 | Debug toolbar |
| `yiisoft/yii2-gii` | ~2.2.0 | Code generator |
| `codeception/codeception` | ^4.0 | Testing framework |
| `codeception/verify` | ~0.5 \|\| ~1.1 | Assertions fluidas |
| `codeception/module-yii2` | ^1.0 | Módulo Yii2 para Codeception |

## Notas de actualización

- `sizeg/yii2-jwt ^2.0` usa la API de `lcobucci/jwt 3.x` que tiene breaking changes en 4.x. Migración requiere reescribir `JwtToken.php`.
- `yiisoft/yii2-swiftmailer` — SwiftMailer fue reemplazado por `symfony/mailer`. La funcionalidad de email no está en uso activo.
