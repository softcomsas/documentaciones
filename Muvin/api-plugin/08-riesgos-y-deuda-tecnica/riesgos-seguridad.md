# Riesgos de Seguridad

## 🔴 CRÍTICO: Credenciales hardcodeadas en ServiciosMuvin.php

**Archivo:** `source/components/Plugin/Cupos/ServiciosMuvin.php`

```php
protected $user = '30700869918';
protected $pass = 'Expo2019!';
protected $url  = 'https://revision1.muvinapp.com/api/';
```

**Riesgo:** Cualquier persona con acceso al repositorio Git tiene las credenciales del usuario de sistema de Muvin. Un atacante podría:
- Acceder directamente a la Muvin API y extraer/modificar datos de cupos
- Escalar privilegios si el usuario tiene permisos amplios en Muvin

**Impacto:** Alto — acceso directo a la plataforma Muvin de producción

**Solución:**
```php
// Mover a variables de entorno
protected $user;
protected $pass;
protected $url;

public function __construct() {
    $this->user = getenv('MUVIN_USER') ?: Yii::$app->params['muvin_user'];
    $this->pass = getenv('MUVIN_PASS') ?: Yii::$app->params['muvin_pass'];
    $this->url  = getenv('MUVIN_URL')  ?: Yii::$app->params['muvin_url'];
}
```

---

## 🔴 CRÍTICO: Ejecución dinámica de código desde BD (call_user_func)

**Archivo:** `source/components/Ejecutor.php`

```php
call_user_func($proc->metodo, $client_conf);
```

**Riesgo:** El campo `proceso.metodo` se obtiene de la base de datos y se ejecuta directamente como función PHP. Si la BD es comprometida (SQL injection, acceso directo), un atacante puede insertar cualquier callable PHP:

```sql
-- Ejemplo de ataque si la BD es comprometida:
UPDATE proceso SET metodo = 'system' WHERE id = 1;
-- Resultado: system($client_conf) → ejecución de comandos del sistema
```

**Impacto:** Crítico — Remote Code Execution (RCE)

**Solución:** Validar el callable contra una whitelist antes de ejecutar:

```php
$allowedMethods = [
    'app\components\Plugin\Cupos\ProcesoAca::DescargarMails',
    'app\components\Plugin\Cupos\ProcesoBunge::DescargarMails',
    // ... lista completa
];

if (!in_array($proc->metodo, $allowedMethods)) {
    Yii::error("Método no permitido: {$proc->metodo}");
    continue;
}

call_user_func($proc->metodo, $client_conf);
```

---

## 🔴 CRÍTICO: AES-128-ECB para encriptación de passwords

**Archivo:** `source/models/Cliente.php`

```php
openssl_encrypt($password, 'AES-128-ECB', $key)
```

**Riesgo:** AES-ECB (Electronic Codebook) es un modo de encriptación inseguro:
- No usa IV (vector de inicialización)
- Bloques iguales en el plaintext producen bloques iguales en el ciphertext
- Vulnerable a ataques de análisis de patrones

**Impacto:** Medio-Alto — los passwords de acceso a Muvin API por cliente podrían ser comprometidos si la BD es accedida

**Solución:** Migrar a AES-256-GCM:
```php
// Encriptar
$iv = random_bytes(12);
$encrypted = openssl_encrypt($password, 'AES-256-GCM', $key, 0, $iv, $tag);
$stored = base64_encode($iv . $tag . $encrypted);

// Desencriptar
$decoded = base64_decode($stored);
$iv = substr($decoded, 0, 12);
$tag = substr($decoded, 12, 16);
$ciphertext = substr($decoded, 28);
$password = openssl_decrypt($ciphertext, 'AES-256-GCM', $key, 0, $iv, $tag);
```

---

## 🔴 ALTO: Endpoints HTTP sin autenticación

**Archivo:** `source/controllers/PluginController.php`

Todos los endpoints `GET /plugin/*` son accesibles sin autenticación. Cualquier persona con acceso a la red puede:
- Disparar el procesamiento de cupos manualmente
- Sincronizar catálogos
- Potencialmente causar procesamiento duplicado

**Solución:** Agregar autenticación HTTP básica o por token en `PluginController::behaviors()`:

```php
public function behaviors() {
    return [
        'authenticator' => [
            'class' => HttpBearerAuth::class,
        ],
    ];
}
```

---

## 🟡 MEDIO: Bypass de password con 'SuperAdmin'

**Archivo:** `source/components/Auth.php`

```php
// esto es para debug. Me logueo con distintos usuarios sin saber la clave..
// En la puesta en marcha eliminar el if.
if ($password !== 'SuperAdmin') {
    if (!$user->validatePassword($password)) {
        throw new UserException('Usuario o clave incorrectos');
    }
}
```

**Riesgo:** Backdoor de autenticación que nunca fue eliminado. Cualquier usuario del sistema puede ser impersonado con la contraseña `'SuperAdmin'`.

**Impacto:** Alto — acceso completo a cualquier cuenta del panel

**Solución:** Eliminar el bloque `if ($password !== 'SuperAdmin')` inmediatamente.

---

## 🟡 MEDIO: IDs hardcodeados en PluginController

**Archivo:** `source/controllers/PluginController.php`

```php
$id_cliente_configuracion = 20; // Tomas Hnos/Cofco
$id_cliente_configuracion = 24; // Tomas Hnos/Dreyfus
$id_cliente_configuracion = 26; // Fyo
```

**Riesgo:** Si los registros en BD cambian de ID (ej: tras restore de backup), los endpoints procesarán la configuración incorrecta sin error visible.

**Solución:** Identificar las configuraciones por un campo único natural (ej: `nombre` o `usermail`) en lugar de ID.

---

## 🟡 BAJO: Email.php.bak en el repositorio

**Archivo:** `source/components/Email.php.bak`

Archivo de backup committado al repositorio. Puede contener credenciales o información sensible de versiones anteriores.

**Solución:** Eliminar del repositorio y del historial de Git:
```bash
git rm source/components/Email.php.bak
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch source/components/Email.php.bak' HEAD
```

---

## 🟡 BAJO: URL apunta a entorno de revisión/staging

**Archivo:** `source/components/Plugin/Cupos/ServiciosMuvin.php`

```php
protected $url = 'https://revision1.muvinapp.com/api/';
```

`revision1` sugiere un entorno de staging. Verificar si el plugin de producción apunta al entorno correcto.

---

## Resumen de riesgos

| ID | Descripción | Severidad | Archivo |
|---|---|---|---|
| SEC-01 | Credenciales hardcodeadas en ServiciosMuvin | 🔴 CRÍTICO | ServiciosMuvin.php |
| SEC-02 | call_user_func desde BD (RCE) | 🔴 CRÍTICO | Ejecutor.php |
| SEC-03 | AES-128-ECB sin IV | 🔴 CRÍTICO | Cliente.php |
| SEC-04 | Endpoints sin autenticación | 🔴 ALTO | PluginController.php |
| SEC-05 | Backdoor 'SuperAdmin' | 🟡 ALTO | Auth.php |
| SEC-06 | IDs hardcodeados | 🟡 MEDIO | PluginController.php |
| SEC-07 | Email.php.bak en repo | 🟡 BAJO | Email.php.bak |
| SEC-08 | URL apunta a staging | 🟡 BAJO | ServiciosMuvin.php |
