# Configuración

> [[requisitos-entorno]]

## Archivos de configuración

| Archivo | Propósito |
|---------|-----------|
| `config/params.php` | Variables de entorno lógico (`ENTORNO_WS`) |
| `config/web.php` | Bootstrap de la app Yii2, auth, componentes |
| `config/db.php` | Conexión MySQL (no versionado — crear desde `db.php.example` si existe) |

## `config/params.php`

```php
return [
    'ENTORNO_WS' => 'TEST',  // 'TEST' | 'PROD'
];
```

### Efecto del entorno

| Valor | WSDL WSAA | WSDL WSCPE |
|-------|-----------|------------|
| `TEST` | `wsaahomo.afip.gov.ar/ws/services/LoginCms?WSDL` | `cpea-ws-qaext.afip.gob.ar/.../...?wsdl` |
| `PROD` | `wsaa.afip.gov.ar/ws/services/LoginCms?WSDL` | `cpea-ws.afip.gob.ar/.../...?wsdl` |

**⚠️ Cambiar a `PROD` requiere un certificado AFIP de producción válido cargado en la tabla `certificado`.**

## `config/web.php` — Variables críticas

| Parámetro | Valor por defecto | Recomendación |
|-----------|-------------------|---------------|
| `cookieValidationKey` | `lFAP6jGr0BdmvFM3mzT48j6JSxO-iyG2` (hardcodeado) | Mover a variable de entorno |
| `enableCsrfValidation` | `false` | Mantener `false` para API REST pura |
| Token TTL | `9 * 3600` segundos | Ajustable en `LoginController` |

## `config/db.php` — Estructura esperada

```php
return [
    'class'    => 'yii\db\Connection',
    'dsn'      => 'mysql:host=localhost;dbname=api_wscpe',
    'username' => 'root',
    'password' => '',
    'charset'  => 'utf8',
];
```

Mover credenciales a variables de entorno usando `getenv()`:
```php
'dsn'      => getenv('DB_DSN'),
'username' => getenv('DB_USER'),
'password' => getenv('DB_PASS'),
```

## Variables de entorno recomendadas

| Variable | Descripción |
|----------|-------------|
| `DB_DSN` | DSN MySQL completo |
| `DB_USER` | Usuario MySQL |
| `DB_PASS` | Contraseña MySQL |
| `COOKIE_VALIDATION_KEY` | Clave secreta Yii2 |
| `ENTORNO_WS` | `TEST` o `PROD` |

## Comandos de operación

```bash
# Aplicar migraciones nuevas
php yii migrate

# Revertir última migración
php yii migrate/down 1

# Ver estado de migraciones
php yii migrate/history
```
