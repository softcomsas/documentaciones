# Modelo de datos — ChoferModel y Preferences

## ChoferModel

Modelo tipado para el cuerpo de la petición de registro/actualización del chofer.

### Estructura

```dart
ChoferModel {
  Persona persona
  Camion  camion
  Acoplado acoplado
  String  centro    // CUIT del centro: "33715514759"
}

Persona {
  String cuitCuil
  String nombre
  String apellidos
  String razonSocial    // "apellidos,nombre"
  String idLocalidad
  String domicilio
  String password       // generado: "P@ssW{cuit}"
  String telefono
  String email
  int    appChoferInstalada   // siempre 1
}

Camion {
  int    idMarcaCamion
  int    idTipoCamion
  int    anno
  String patente
}

Acoplado {
  int    idMarcaAcoplado
  int    idTipoAcoplado
  int    anno
  String patente
}
```

### JSON de ejemplo

```json
{
  "persona": {
    "cuit_cuil": "20123456789",
    "nombre": "Juan",
    "apellidos": "Pérez",
    "razon_social": "Pérez,Juan",
    "id_localidad": "45",
    "domicilio": "Av. Corrientes 1234",
    "password": "P@ssW20123456789",
    "telefono": "1122334455",
    "email": "juan@example.com",
    "app_chofer_instalada": 1
  },
  "camion": {
    "id_marca_camion": 3,
    "id_tipo_camion": 1,
    "patente": "ABC123"
  },
  "acoplado": {
    "id_marca_acoplado": 2,
    "id_tipo_acoplado": 4,
    "patente": "DEF456"
  },
  "centro": "33715514759"
}
```

## SharedPreferences — Preferences class

La clase `Preferences` (singleton) persiste el estado de la sesión y el viaje activo mediante `SharedPreferences`.

### Campos almacenados

| Clave SharedPrefs | Tipo | Descripción |
|------------------|------|-------------|
| `nombre` | String | Nombre del chofer |
| `apellidos` | String | Apellidos del chofer |
| `razon_social` | String | Razón social |
| `cuit_cuil` | String | CUIT/CUIL |
| `dni` | String | DNI |
| `telefono` | String | Teléfono de contacto |
| `email` | String | Email |
| `domicilio` | String | Domicilio |
| `disponible` | Int | 0 = no disponible, 1 = disponible |
| `id_chofer` | String | ID del chofer en el backend |
| `patente_camion` | String | Patente del camión |
| `id_tipo_camion` | String | ID del tipo de camión |
| `id_marca_camion` | String | ID de la marca del camión |
| `anno_camion` | String | Año del camión |
| `patente_acoplado` | String | Patente del acoplado |
| `id_tipo_acoplado` | String | ID del tipo de acoplado |
| `id_marca_acoplado` | String | ID de la marca del acoplado |
| `anno_acoplado` | String | Año del acoplado |
| `access_token` | String | Bearer token de autenticación |
| `ultimaPagina` | String | Última ruta visitada (default: 'login') |
| `id_viaje` | String | ID del viaje activo |
| `id_destino` | String | ID del destino del viaje |
| `cupo` | String | Cupo asignado |
| `id_estado` | String | Estado del viaje activo |
| `tipo_pedido` | String | Tipo de pedido del viaje |
| `id_consulta` | String | ID de la última consulta activa |
| `seguro` | String | Datos del seguro del viaje |
| `latitude` | String | Última latitud GPS conocida |
| `longitude` | String | Última longitud GPS conocida |
| `producto` | String | Producto del viaje activo |
| `token_notificacion` | String | Token FCM del dispositivo |
| `pais` | String | ID del país (default: '1') |
| `provincia` | String | ID de la provincia |
| `localidad` | String | ID de la localidad |
| `nombreDestino` | String | Nombre del destino del viaje |
| `turneada` | String | Flag/ID del turno del viaje activo |

### Inicialización

```dart
// En main.dart — debe llamarse antes de usar cualquier campo
await Preferences().initPrefs();
```

---

Ver también: [[endpoints-rest]] · [[arranque]] · [[autenticacion-sms]]
