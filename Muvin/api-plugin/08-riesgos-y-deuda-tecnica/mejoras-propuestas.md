# Mejoras Propuestas

## Prioridad Alta — Seguridad

### MEJ-01: Mover credenciales a variables de entorno

**Afecta:** `ServiciosMuvin.php`  
**Esfuerzo:** Bajo (1-2 horas)

```php
// Antes (hardcodeado)
protected $user = '30700869918';
protected $pass = 'Expo2019!';
protected $url  = 'https://revision1.muvinapp.com/api/';

// Después (variable de entorno)
public function __construct() {
    $this->user = getenv('MUVIN_USER');
    $this->pass = getenv('MUVIN_PASS');
    $this->url  = getenv('MUVIN_URL');
}
```

Agregar al `docker-compose.yaml`:
```yaml
environment:
  - MUVIN_USER=30700869918
  - MUVIN_PASS=<secret>
  - MUVIN_URL=https://app.muvinapp.com/api/
```

---

### MEJ-02: Whitelist para call_user_func

**Afecta:** `Ejecutor.php`  
**Esfuerzo:** Bajo (2-3 horas)

```php
private static $allowedMethods = [
    \app\components\Plugin\Cupos\ProcesoAca::class . '::DescargarMails',
    \app\components\Plugin\Cupos\ProcesoBunge::class . '::DescargarMails',
    \app\components\Plugin\Cupos\ProcesoCofco::class . '::DescargarMails',
    \app\components\Plugin\Cupos\ProcesoDreyfus::class . '::DescargarMails',
    \app\components\Plugin\Cupos\ProcesoFyo::class . '::DescargarMails',
    \app\components\Plugin\Cupos\ProcesoMolinosAgro::class . '::DescargarMails',
    \app\components\Plugin\Cupos\ProcesoSamsa::class . '::DescargarMails',
];

// Validar antes de ejecutar
if (!in_array($proc->metodo, self::$allowedMethods, true)) {
    Yii::error("Método no permitido bloqueado: {$proc->metodo}");
    continue;
}
```

---

### MEJ-03: Eliminar backdoor SuperAdmin

**Afecta:** `Auth.php`  
**Esfuerzo:** Mínimo (5 minutos)

```php
// Eliminar completamente este bloque:
if ($password !== 'SuperAdmin') {
    if (!$user->validatePassword($password)) {
        throw new UserException('Usuario o clave incorrectos');
    }
}

// Reemplazar con:
if (!$user->validatePassword($password)) {
    throw new UserException('Usuario o clave incorrectos');
}
```

---

### MEJ-04: Autenticación en endpoints de PluginController

**Afecta:** `PluginController.php`  
**Esfuerzo:** Bajo (2-3 horas)

```php
public function behaviors() {
    return array_merge(parent::behaviors(), [
        'authenticator' => [
            'class' => HttpBearerAuth::class,
            'except' => [], // todos los endpoints requieren auth
        ],
    ]);
}
```

---

## Prioridad Media — Arquitectura

### MEJ-05: Panel de administración de clientes

Actualmente la alta de clientes requiere ejecutar migraciones manualmente. Construir endpoints CRUD (o integrarlos al panel de administración de Muvin):

```
POST   /admin/clientes                  → crear cliente
GET    /admin/clientes                  → listar clientes
PUT    /admin/clientes/{id}             → editar cliente
DELETE /admin/clientes/{id}             → desactivar cliente
POST   /admin/clientes/{id}/configuraciones → agregar configuración IMAP
POST   /admin/clientes/{id}/procesos    → agregar proceso
```

---

### MEJ-06: Control de concurrencia con flock

**Afecta:** `cron/plugin_cupos`  
**Esfuerzo:** Mínimo (5 minutos)

```bash
*/15 * * * * flock -n /tmp/plugin_lector.lock sh -c 'cd /app && php yii parametros && php yii lector' >> /var/log/cron.log 2>&1
```

---

### MEJ-07: Tests para los parsers

Implementar tests con Codeception para verificar que cada parser extrae correctamente los datos de sus formatos:

```
tests/
├── unit/
│   ├── LeerPdfTest.php          ← fixtures de PDFs ACA
│   ├── LeerHtmlBungueTest.php
│   ├── LeerHtmlCofcoTest.php
│   └── ...
└── fixtures/
    ├── aca_cupo_ejemplo.pdf
    ├── bunge_cupo_ejemplo.html
    └── ...
```

---

### MEJ-08: Migrar encriptación a AES-256-GCM

**Afecta:** `Cliente.php` + migración de BD  
**Esfuerzo:** Medio (1-2 días incluyendo migración de datos)

Migración necesaria:
1. Desencriptar todos los passwords existentes (AES-128-ECB)
2. Re-encriptarlos con AES-256-GCM
3. Actualizar el código de `Cliente.php`

---

## Prioridad Baja — Mantenimiento

### MEJ-09: Reducir frecuencia de sincronización de catálogos

Los catálogos (productos, destinos, CUITs) cambian con baja frecuencia. Sincronizarlos cada 15 minutos es excesivo:

```bash
# Catálogos: 1 vez por día (a las 2 AM)
0 2 * * * cd /app && php yii parametros >> /var/log/cron.log 2>&1

# Solo procesamiento: cada 15 minutos
*/15 * * * * flock -n /tmp/plugin.lock php /app/yii lector >> /var/log/cron.log 2>&1
```

---

### MEJ-10: Activar limpieza automática de PDFs

Descomentar la tarea de limpieza de PDFs en el cron:

```bash
0 3 * * * find /app/pdfs -name '*.pdf' -mtime +30 -exec rm {} \;
```

---

### MEJ-11: Resolver IDs hardcodeados en PluginController

Identificar las configuraciones Tomas Hnos por un campo natural:

```php
// Antes (hardcodeado)
$id_cliente_configuracion = 20;

// Después (lookup por nombre único)
$cliente_configuracion = ClienteConfiguracion::find()
    ->joinWith('cliente')
    ->where(['cliente.nombre' => 'Tomas Hnos', 'proceso.nombre' => 'Cofco'])
    ->all();
```

---

## Backlog resumido

| ID | Mejora | Prioridad | Esfuerzo |
|---|---|---|---|
| MEJ-01 | Credenciales a variables de entorno | 🔴 Alta | Bajo |
| MEJ-02 | Whitelist call_user_func | 🔴 Alta | Bajo |
| MEJ-03 | Eliminar backdoor SuperAdmin | 🔴 Alta | Mínimo |
| MEJ-04 | Auth en endpoints plugin | 🔴 Alta | Bajo |
| MEJ-05 | Panel de administración | 🟡 Media | Alto |
| MEJ-06 | flock en cron | 🟡 Media | Mínimo |
| MEJ-07 | Tests para parsers | 🟡 Media | Medio |
| MEJ-08 | AES-256-GCM | 🟡 Media | Medio |
| MEJ-09 | Reducir freq. catálogos | 🟢 Baja | Mínimo |
| MEJ-10 | Activar limpieza PDFs | 🟢 Baja | Mínimo |
| MEJ-11 | Eliminar IDs hardcodeados | 🟢 Baja | Bajo |
