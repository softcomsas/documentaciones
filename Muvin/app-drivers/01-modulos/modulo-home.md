# Módulo: Home

> **Ruta:** `lib/src/pages/home_page.dart`
> **Criticidad:** 🔴 Alta
> **Estado:** Activo

## Propósito

Dashboard principal del chofer. Muestra botones de acceso a las secciones principales y **reporta la posición GPS cada 10 segundos** al backend mientras el chofer tiene la pantalla abierta.

## Funcionalidades

| # | Funcionalidad | Descripción |
|---|---------------|-------------|
| 1 | Tracking GPS | Timer cada 10s: obtiene posición y llama `actualizarPosicion` |
| 2 | Carga datos iniciales | Llama `obtenerMiViaje` y habilita botones según estado del viaje |
| 3 | Navegación a Cargas | Botón → `/cargas` |
| 4 | Navegación a Mi Viaje | Botón habilitado si hay viaje asignado → `/viaje` |
| 5 | Navegación a Carta/Remito | Botón habilitado si `pdf == 0` → `/cartaporte` |
| 6 | Navegación a Descarga | Botón habilitado si `pdf == 1` → sube remito final |
| 7 | Subida de imagen (ImagePicker) | Usa `image_picker` para seleccionar/tomar foto |

## Lógica de habilitación de botones

```dart
_habilitarViaje = true;                          // Tiene viaje asignado
_habilitarCarta = (dataViaje['pdf'] == 1) ? false : true;   // pdf=0: carta pendiente
_habilitarDescarga = (dataViaje['pdf'] == 1) ? true : false; // pdf=1: ya subió remito
```

## Timer GPS

```dart
Timer.periodic(Duration(seconds: 10), (timer) {
    _geolocation.getCurrentLocation();
    if (_geolocation.currentPosition != null) {
        muvinProvider.actualizarPosicion({ latitude, longitude });
    }
});
```

> ⚠️ El `Timer` se crea en `initState` pero **nunca se cancela** en `dispose`. Si el widget se destruye, el timer sigue corriendo y puede causar memory leaks o llamadas a `setState` sobre un widget desmontado.

## Dependencias

- **Depende de:** [[modulo-muvin-provider]], [[modulo-geolocator]]
- **Endpoints:** `GET chofer-app/mi-viaje`, `POST chofer-app/actualizar-posicion`

## Riesgos

- 🔴 Timer no cancelado → posible memory leak y crash (`setState on unmounted widget`)
- ⚠️ `FutureBuilder` llama `_cargarDatosIniciales()` en cada `rebuild` — puede hacer múltiples llamadas HTTP innecesarias
- ⚠️ `geolocator ^5.1.3` usa API obsoleta (`Geolocator()..forceAndroidLocationManager`)
