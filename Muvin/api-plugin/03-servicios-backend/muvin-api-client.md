# Cliente HTTP — ServiciosMuvin

**Ruta:** `source/components/Plugin/Cupos/ServiciosMuvin.php`

## Propósito

`ServiciosMuvin` encapsula todas las llamadas HTTP a la Muvin API REST. Es utilizado por los procesos `Proceso*.php` para autenticarse y cargar los cupos procesados.

## Configuración

> 🔴 **CRÍTICO:** Las credenciales están hardcodeadas en el código fuente.

```php
class ServiciosMuvin {
    protected $user = '30700869918';           // CUIT del usuario de sistema
    protected $pass = 'Expo2019!';             // Password en texto plano
    protected $url  = 'https://revision1.muvinapp.com/api/';
}
```

## Patrón Singleton por usuario

La clase implementa un patrón de instancia única por usuario para reutilizar el token de sesión:

```php
private static $instances = [];

public static function getInstance($user, $pass) {
    $key = $user . ':' . $pass;
    if (!isset(self::$instances[$key])) {
        self::$instances[$key] = new self($user, $pass);
    }
    return self::$instances[$key];
}
```

## Métodos

### `login()`
- **POST** `{url}site/login`
- Autentica con `$user` y `$pass`
- Guarda el token JWT retornado para las siguientes llamadas
- Lanza excepción si el login falla

### `cargarCupo(objCupoMuvin $cupo)`
- **POST** `{url}cupos/cargar` (o similar)
- Envía el payload del cupo al endpoint de carga de Muvin
- Retorna la respuesta de la API
- El payload es construido desde `objCupoMuvin`

### `verProductos()`
- **GET** `{url}productos` (o similar)
- Retorna el listado de productos/granos disponibles en Muvin

### `verDestinos()`
- **GET** `{url}destinos` (o similar)
- Retorna el listado de destinos (plantas) disponibles

### `verCuitEmpresas()`
- **GET** `{url}cuit-empresas` (o similar)
- Retorna los CUITs de las empresas registradas en Muvin

## Flujo de uso típico

```php
// En ProcesoAca::CargarMuvin()
$servicio = ServiciosMuvin::getInstance($cliente->user_muvin, $cliente->getPassword());
$servicio->login();

$cupoObj = new objCupoMuvin($cupoDocumento);
$response = $servicio->cargarCupo($cupoObj);
```

## ServiciosDescargas

**Ruta:** `source/components/Plugin/Cupos/ServiciosDescargas.php`

Variante de `ServiciosMuvin` específica para las operaciones de `DescargasRT`. Maneja los endpoints de la Muvin API relacionados con descargas en tiempo real (COE).

## URL del entorno

La URL `https://revision1.muvinapp.com/api/` corresponde al entorno de **revisión/staging**. Verificar si en producción se usa otra URL.

> ⚠️ Si el sistema está apuntando siempre a `revision1`, podría no estar usando el entorno de producción real.
