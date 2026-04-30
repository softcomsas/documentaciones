# Inventario de Seguridad — api-wscpe

> [[README]]

## Resumen ejecutivo

| Severidad | Cantidad |
|-----------|---------|
| 🔴 Crítico | 5 |
| 🟠 Alto | 3 |
| ⚠️ Medio | 3 |

---

## Hallazgos Críticos

### SEC-01 · Token de sesión generado con MD5(uniqid())
- **Archivo:** `controllers/LoginController.php`
- **Código:** `$user->token = md5(uniqid());`
- **Impacto:** MD5 no es criptográficamente seguro. `uniqid()` tiene entropía baja basada en el timestamp del servidor. Un atacante con acceso al servidor puede predecir o colisionar tokens.
- **Mitigación:** Usar `bin2hex(random_bytes(32))` para generar tokens seguros.

### SEC-02 · Certificado privado X.509 almacenado en texto plano en BD
- **Archivo:** `controllers/CertificadoController.php`, `models/Certificado.php`
- **Impacto:** Cualquier persona con acceso a la BD puede leer la clave privada y la passphrase del certificado AFIP. Esto permite suplantar al operador ante AFIP.
- **Mitigación:** Cifrar `cert`, `key` y `passphrase` con AES-256 antes de guardar en BD. Usar la clave de cifrado en variable de entorno del servidor.

### SEC-03 · Certificados escritos en disco como archivos planos
- **Archivo:** `models/Certificado.php` → `descargarCertificado()`
- **Código:** `file_put_contents(basePath . "/Afip_res/cert.key", $cert->cert)`
- **Impacto:** Si `Afip_res/` es accesible por el servidor web, los certificados son descargables públicamente.
- **Mitigación:** Configurar el servidor web para bloquear el acceso directo a `Afip_res/`. Mover la escritura a `/tmp/` o usar un directorio fuera del webroot.

### SEC-04 · CSRF deshabilitado globalmente
- **Archivo:** `config/web.php`
- **Código:** `'enableCsrfValidation' => false`
- **Impacto:** Aunque es una API REST (no una app web tradicional), deshabilitar CSRF globalmente elimina una capa de protección.
- **Mitigación:** Mantener deshabilitado solo en los endpoints REST; asegurarse que el token Bearer en cada request actúa como protección equivalente.

### SEC-05 · `cookieValidationKey` hardcodeado en config
- **Archivo:** `config/web.php`
- **Valor:** `'cookieValidationKey' => 'lFAP6jGr0BdmvFM3mzT48j6JSxO-iyG2'`
- **Impacto:** La clave de validación de cookies está en el código fuente. Si se conoce, se pueden forjar cookies.
- **Mitigación:** Mover a variable de entorno. Rotar inmediatamente si fue expuesta en repositorio público.

---

## Hallazgos Altos

### SEC-06 · Sin rate limiting en endpoint de login
- **Impacto:** El endpoint `POST /login/login` es públicamente accesible y no tiene ningún límite de intentos. Vulnerable a ataques de fuerza bruta sobre contraseñas de usuarios.
- **Mitigación:** Implementar rate limiting (ej. con nginx `limit_req` o middleware Yii2).

### SEC-07 · Control de acceso admin por string hardcodeado
- **Archivo:** `CertificadoController.php`, `UserController.php`
- **Código:** `if(Yii::$app->user->identity->username != 'admin')`
- **Impacto:** No usa el RBAC de Yii2. Si se renombra el usuario admin o se añaden roles, este control falla silenciosamente.
- **Mitigación:** Implementar RBAC con roles y permisos de Yii2.

### SEC-08 · Swagger UI público en producción
- **Endpoint:** `GET /swagger`
- **Impacto:** Expone la estructura completa de la API, parámetros y ejemplos a cualquier visitante.
- **Mitigación:** Proteger con autenticación básica en producción o deshabilitar.

---

## Hallazgos Medios

### SEC-09 · PHP 7.4 EOL
- PHP 7.4 no recibe parches de seguridad desde noviembre 2022. Cualquier vulnerabilidad descubierta en PHP 7.4 queda sin parchear.

### SEC-10 · Sin HTTPS forzado
- No hay redirección forzada de HTTP a HTTPS en la configuración. Las credenciales de login y los certificados AFIP podrían transmitirse en texto claro.

### SEC-11 · Condición de carrera en escritura de certificados
- Si dos requests llegan simultáneamente, ambas escriben `cert.key` y `key.pem` concurrentemente. Una puede sobreescribir mientras la otra está usando el archivo.
