# Inventario de Dependencias

> **Sección:** 05-inventarios
> **Proyecto:** app-drivers
> **SDK:** Dart >=2.1.0 <3.0.0 | Flutter 1.x

Todas las dependencias son de la era Flutter 1.x (2019-2020). Ninguna tiene soporte activo para Flutter 3+/null safety.

## Dependencias de Producción

| Paquete | Versión | Propósito | Estado |
|---------|---------|-----------|--------|
| `flutter` | sdk | Framework | 🔴 Flutter 1.x — EOL |
| `http` | ^0.12.1 | Peticiones REST | 🔴 Obsoleto (actual: 1.x) |
| `shared_preferences` | ^0.5.6 | Almacenamiento local | 🔴 Obsoleto (actual: 2.x) |
| `geolocator` | ^5.1.3 | GPS/Geolocalización | 🔴 Obsoleto (actual: 11.x) |
| `flutter_swiper` | ^1.1.6 | Carrusel de tarjetas | 💀 Abandonado |
| `rxdart` | ^0.22.6 | Streams reactivos BLoC | 🔴 Obsoleto (actual: 0.27.x) |
| `image_picker` | ^0.6.7 | Cámara/galería | 🔴 Obsoleto (actual: 1.x) |
| `flutter_map` | ^0.9.0 | Mapas (MapBox) | 🔴 Obsoleto (actual: 6.x) |
| `latlong` | ^0.6.1 | Coordenadas lat/lon | 🔴 Obsoleto |
| `permission_handler` | ^4.4.0 | Permisos del dispositivo | 🔴 Obsoleto (actual: 11.x) |
| `intl` | ^0.16.0 | Internacionalización | 🔴 Obsoleto (actual: 0.19.x) |
| `flutter_localizations` | sdk | Localización Flutter | 🔴 Flutter 1.x |
| `font_awesome_flutter` | ^8.8.1 | Íconos Font Awesome | 🔴 Obsoleto (actual: 10.x) |

## Dependencias de Desarrollo

| Paquete | Versión | Propósito | Estado |
|---------|---------|-----------|--------|
| `flutter_test` | sdk | Testing | 🔴 Flutter 1.x |

## Impacto de Obsolescencia

- **Sin null safety:** Dart <2.12 — imposible migrar a Flutter 3+ sin refactor.
- **APIs rotas:** `geolocator 5.x` y `image_picker 0.6.x` tienen APIs completamente distintas a sus versiones actuales.
- **Vulnerabilidades:** `http 0.12.1` puede tener CVEs no parcheados.
- **Play Store / App Store:** Google y Apple pueden rechazar binarios compilados con Flutter 1.x.
