# Entidad: Viaje

> **Origen:** respuesta `GET chofer-app/mi-viaje`

## Descripción

Representa el viaje actualmente asignado al chofer. Puede ser `null` si no hay viaje asignado.

## Shape del objeto

```json
{
  "id": 42,
  "origen": "Rosario, Santa Fe",
  "destino": "Buenos Aires, CABA",
  "fechaCarga": "2024-03-15",
  "cliente": "Empresa XYZ",
  "tipoCarga": "Granel seco",
  "estado": "en_camino",
  "pdf": "https://cdn.muvinapp.com/remitos/42.pdf"
}
```

## Campos clave

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int | Identificador del pedido asignado |
| `origen` | String | Dirección de origen/carga |
| `destino` | String | Dirección de destino/entrega |
| `cliente` | String | Destinatario |
| `estado` | String | Estado del viaje |
| `pdf` | String\|null | URL del documento. **Si existe**, habilita botón en `HomePage` |

## Lógica de negocio

El campo `pdf` controla la habilitación del botón "Ver mi viaje" en `HomePage`:

```dart
bool botonHabilitado = miViaje != null && miViaje['pdf'] != null;
```

## Entidad: Posición GPS

> **Origen:** `GeolocatorProvider.currentPosition` → tipo `Position` de `geolocator ^5.1.3`

```dart
class GeolocatorProvider {
  Position currentPosition;

  Future getCurrentLocation() async {
    currentPosition = await Geolocator().getCurrentPosition(
      desiredAccuracy: LocationAccuracy.high
    );
  }
}
```

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `latitude` | double | Latitud WGS84 |
| `longitude` | double | Longitud WGS84 |

Estos valores se envían directamente a `actualizarPosicion(lat, lon)`.
