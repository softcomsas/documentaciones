# Dependencias y Clasificación — api-wscpe

> [[README]] · [[tree-estructura-archivos]]

## Matriz de dependencias

| Paquete | Versión | Tipo | Estado |
|---------|---------|------|--------|
| `yiisoft/yii2` | `~2.0.45` | Producción | ⚠️ Mantenimiento (no hay v3) |
| `yiisoft/yii2-bootstrap5` | `~2.0.2` | Producción | ✅ |
| `yiisoft/yii2-symfonymailer` | `~2.0.3` | Producción | ✅ (no usado activamente) |
| `light/yii2-swagger` | `~1.0.4` | Producción | ⚠️ Genera Swagger 2.0 (no OpenAPI 3) |
| `phpunit/phpunit` | `~9.5.0` | Dev | ✅ |
| `codeception/codeception` | `^5.0` | Dev | ✅ |

## PHP built-in (sin paquete externo)

| Componente | Uso |
|-----------|-----|
| `SoapClient` | Llamadas WSCPE AFIP |
| `file_put_contents` | Escritura de certificados |
| `json_encode/decode` | Serialización de caché |
| `md5`, `uniqid` | Generación de tokens (🔴 inseguro) |
| `openssl` (extensión PHP) | Procesamiento X.509 en `Afip.php` |
| `HtmlPurifier` (Yii2) | Sanitización de inputs |

## Clasificación funcional de archivos

### Críticos para la operación

| Archivo | Rol |
|---------|-----|
| `web/index.php` | Entry point HTTP |
| `controllers/CpeController.php` | Endpoint principal |
| `components/Afipws.php` | SOAP AFIP |
| `components/Afip.php` | WSAA AFIP |
| `models/QueryCache.php` | Caché + orquestación |
| `models/Certificado.php` | Gestión certificados |
| `config/web.php` | Configuración central |
| `config/params.php` | Entorno TEST/PROD |

### Administrativos

| Archivo | Rol |
|---------|-----|
| `controllers/LoginController.php` | Auth |
| `controllers/CertificadoController.php` | Gestión cert |
| `controllers/UserController.php` | Gestión usuarios |

### Scaffolding / no operacionales

| Archivo | Rol |
|---------|-----|
| `controllers/SiteController.php` | Template Yii2 básico |
| `controllers/DefaultController.php` | Template |
| `models/ContactForm.php` | No usado |
| `views/` | Mínimas (solo error pages) |
