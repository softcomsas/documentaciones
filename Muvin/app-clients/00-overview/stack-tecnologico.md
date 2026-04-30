# Stack Tecnológico — App Clients

> **Última revisión:** 2026-04-30

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|------------|---------|-----------|---------------|--------|
| Flutter | ~2.x | Framework móvil multiplataforma | ⚠️ Flutter 2.x EOL (actual: 3.x) | 🔴 |
| Dart | >=2.1.0 <3.0.0 | Lenguaje de programación | ⚠️ Dart 2.x EOL (actual: 3.x) | 🔴 |
| rxdart | ^0.22.6 | Programación reactiva (BLoC) | Versión antigua (actual: 0.27.x) | 🟡 |
| http | ^0.12.0 | Cliente HTTP | Versión antigua (actual: 1.x) | 🟡 |
| firebase_core | ^1.10.0 | Firebase base | Vigente (v3.x actual) — actualizar | 🟡 |
| firebase_analytics | ^5.0.11 | Analytics | Versión antigua | 🟡 |
| firebase_messaging | ^6.0.12 | Push notifications (FCM) | Versión muy antigua | 🔴 |
| shared_preferences | ^0.5.6 | Persistencia local (tokens, prefs) | Versión antigua (actual: 2.x) | 🟡 |
| socket_io_client | ^0.9.8 | WebSocket / tiempo real | Versión antigua (actual: 2.x) | 🟡 |
| geolocator | ^5.1.3 | Geolocalización | Versión antigua (actual: 11.x) | 🟡 |
| barcode_scan | ^3.0.1 | Escáner de códigos QR/barras | ⚠️ Paquete deprecado | 🔴 |
| barcode_flutter | ^1.1.0 | Generación de códigos de barras | ⚠️ Paquete sin mantenimiento | 🔴 |
| flutter_map | ^0.8.2 | Mapas (Leaflet/OpenStreetMap) | Versión muy antigua (actual: 7.x) | 🟡 |
| image_picker | ^0.6.1+4 | Selección de imágenes | Versión antigua (actual: 1.x) | 🟡 |
| flutter_typeahead | ^3.1.0 | Autocompletado | Vigente | 🟢 |
| permission_handler | ^8.3.0 | Permisos del SO | Versión antigua (actual: 11.x) | 🟡 |
| pin_code_text_field | ^1.4.0 | Campo PIN | ⚠️ Sin mantenimiento activo | 🟡 |
| pdf | ^1.4.1 | Generación de PDFs | Versión muy antigua (actual: 3.x) | 🟡 |
| flutter_html | ^0.11.0 | Renderizado HTML | Versión antigua (actual: 3.x) | 🟡 |
| font_awesome_flutter | ^8.5.0 | Iconos FontAwesome | Vigente | 🟢 |
| connectivity | ^3.0.6 | Estado de conexión | ⚠️ Deprecado → usar `connectivity_plus` | 🔴 |
| sweetalert | any | Alertas modales | ⚠️ Sin versión fija, sin mantenimiento | 🔴 |
| bubble | ^1.1.9+1 | Widgets de chat bubble | Sin mantenimiento activo | 🟡 |
| modal_progress_hud | ^0.1.3 | Loading overlay | Sin mantenimiento activo | 🟡 |
| flutter_swiper | ^1.1.6 | Swiper/carousel | ⚠️ Deprecado | 🔴 |
| flutter_sequence_animation | ^3.0.0 | Animaciones secuenciales | Sin mantenimiento | 🟡 |

> [!danger] Flutter/Dart desactualizados
> El SDK constraint `>=2.1.0 <3.0.0` bloquea la app a Flutter 2.x, que está fuera de soporte. Migrar a Flutter 3.x / Dart 3.x es la acción de mayor impacto para estabilidad y acceso a nuevas APIs del SO.

> [!warning] Paquetes deprecados críticos
> `barcode_scan`, `connectivity` y `flutter_swiper` están oficialmente deprecados y sin mantenimiento. Deben reemplazarse antes de cualquier actualización del SDK.
