# Índice de Entidades / Modelo de Datos

> **Sección:** 04-modelo-de-datos
> **Proyecto:** app-drivers

La app no usa modelos de datos tipados (clases Dart). Todos los datos del backend se manejan como `dynamic` / `Map<String, dynamic>` (JSON sin parsear a clases).

| # | Entidad | Descripción | Archivo |
|---|---------|-------------|---------|
| 1 | [Preferencias (SharedPreferences)](./entidad-preferences.md) | Estado local del usuario | preference.dart |
| 2 | [Pedido / Carga](./entidad-pedido.md) | Pedido de transporte | respuesta API |
| 3 | [Viaje](./entidad-viaje.md) | Viaje asignado al chofer | respuesta API |
| 4 | [Posición GPS](./entidad-posicion.md) | Coordenadas del chofer | GeolocatorProvider |
