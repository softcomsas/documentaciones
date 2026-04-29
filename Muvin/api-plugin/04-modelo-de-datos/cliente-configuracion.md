# Modelo: ClienteConfiguracion

**Archivo:** `source/models/ClienteConfiguracion.php`  
**Tabla:** `cliente_configuracion`

## Descripción

Representa una cuenta de correo electrónico IMAP asociada a un cliente. Contiene las credenciales para conectarse al servidor de email y descargar las notificaciones de cupos. Un cliente puede tener múltiples configuraciones (una por empresa cerealera o cuenta de correo).

## Campos

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | int PK | Identificador único |
| `usermail` | string | Usuario del servidor de correo (email) |
| `pwdmail` | string | Contraseña del servidor de correo |
| `srvmail` | string | Servidor IMAP (host) |
| `activo` | tinyint | 1 = activa, 0 = inactiva |
| `cliente_id` | int FK | Referencia a `cliente.id` |
| `proceso_id` | int FK | Referencia a `proceso.id` |

## Métodos de clase (ActiveRecord)

### `getConfiguracionByProcesoId($proceso_id)`
Retorna todas las configuraciones activas asociadas a un proceso:
```php
ClienteConfiguracion::getConfiguracionByProcesoId($proceso->id)
```

### `getConfiguracion($id)`
Retorna una configuración específica por ID:
```php
ClienteConfiguracion::getConfiguracion($id)
```
Usado por los endpoints con IDs hardcodeados (Tomas Hnos, Fyo).

## Relaciones

- `belongsTo(Cliente::class, ['cliente_id' => 'id'])`
- `belongsTo(Proceso::class, ['proceso_id' => 'id'])`

## Uso en el flujo

La `ClienteConfiguracion` es el objeto que se pasa a cada método `DescargarMails()`:

```php
// En Ejecutor::correrProcesos()
call_user_func($proc->metodo, $client_conf);

// En ProcesoAca::DescargarMails(ClienteConfiguracion $client_conf)
$imap = imap_open(
    '{' . $client_conf->srvmail . ':993/imap/ssl}INBOX',
    $client_conf->usermail,
    $client_conf->pwdmail
);
```

## Seguridad

> ⚠️ Las contraseñas de correo (`pwdmail`) se almacenan en texto plano o con encriptación básica en la BD. Verificar el esquema real.
