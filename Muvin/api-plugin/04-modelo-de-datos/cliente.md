# Modelo: Cliente

**Archivo:** `source/models/Cliente.php`  
**Tabla:** `cliente`

## Descripción

Representa una empresa cliente que usa el plugin. Cada cliente tiene sus propias credenciales para acceder a la Muvin API y puede tener múltiples configuraciones de correo y procesos asociados.

## Campos

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | int PK | Identificador único |
| `nombre` | string | Nombre de la empresa |
| `cuit` | string | CUIT de la empresa |
| `user_muvin` | string | Usuario para autenticarse en Muvin API |
| `password` | string | Password encriptado (AES-128-ECB) para Muvin API |
| `activo` | tinyint | 1 = activo, 0 = inactivo |

## Encriptación de password

> ⚠️ **Riesgo de seguridad**: se usa AES-128-ECB, modo inseguro (sin IV).

```php
// Encriptar
openssl_encrypt($password, 'AES-128-ECB', $key)

// Desencriptar (usado en tiempo de ejecución para login a Muvin)
openssl_decrypt($this->password, 'AES-128-ECB', $key)
```

La clave de encriptación (`$key`) proviene de la configuración de la aplicación Yii.

## Relaciones

- `hasMany(ClienteConfiguracion::class, ['cliente_id' => 'id'])`
- `hasMany(Proceso::class, ['cliente_id' => 'id'])`

## Uso en el flujo

```php
// En el proceso de carga a Muvin:
$cliente = $client_conf->cliente;
$user = $cliente->user_muvin;
$pass = $cliente->getPassword(); // desencripta AES
$servicio = ServiciosMuvin::getInstance($user, $pass);
```
