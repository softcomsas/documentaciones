# Inventario de Dependencias

**Archivo:** `source/composer.json`

## Dependencias principales (inferidas del stack)

| Paquete | Versión | Uso |
|---|---|---|
| `yiisoft/yii2` | ~2.0 | Framework PHP principal |
| `yiisoft/yii2-swiftmailer` | ~2.0 | Envío de emails |
| `zircote/swagger-php` | ~2.0 | Generación de Swagger |
| `firebase/php-jwt` | ~5.0 | Manejo de tokens JWT |
| `phpoffice/phpspreadsheet` | ~1.x | Lectura de archivos Excel (LeerExcel) |
| `smalot/pdfparser` | ~0.x | Parseo de PDFs (LeerPdf) |

## PHP Extensions requeridas

| Extensión | Uso |
|---|---|
| `php-imap` | Conexión a servidores IMAP para descargar emails |
| `php-mbstring` | Manejo de strings multibyte (UTF-8) |
| `php-openssl` | Encriptación AES de passwords |
| `php-pdo` | Conexión a MySQL |
| `php-curl` | Llamadas HTTP a Muvin API |
| `php-dom` | Parseo de HTML (DOMDocument) |
| `php-xml` | Parseo XML/HTML |

## Herramientas de desarrollo

| Herramienta | Descripción |
|---|---|
| `codeception/codeception` | Framework de testing |
| `Vagrantfile` | Entorno de desarrollo virtual (legacy, no usar) |
| `.bowerrc` | Bower para assets frontend (legacy) |

## Runtime

| Requisito | Versión mínima |
|---|---|
| PHP | 7.4+ (recomendado 8.x) |
| MySQL | 5.7+ |
| Docker | 20.x |

> Para la lista exacta de dependencias, consultar `source/composer.json` y `source/composer.lock`.
