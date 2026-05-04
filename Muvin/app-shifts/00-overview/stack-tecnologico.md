# Stack tecnológico — app-shifts

## Framework y lenguaje

| Tecnología | Versión | Estado |
|-----------|---------|--------|
| Flutter | 1.x | ⚠️ EOL — no recibe updates |
| Dart | ≥2.1.0 <3.0.0 | ⚠️ Sin null safety |
| Plataforma objetivo | Android | iOS no verificado |

## Dependencias de producción

### UI y layout
| Paquete | Versión | Uso |
|---------|---------|-----|
| `flutter` | SDK | Framework base |
| `bubble` | ^1.1.9+1 | Burbujas de chat en consultas |
| `sweetalert` | any | Diálogos de alerta estilizados |
| `flutter_sequence_animation` | ^3.0.0 | Animaciones secuenciales |
| `flutter_html` | ^0.11.0 | Render de HTML en novedades |

### Estado y reactividad
| Paquete | Versión | Uso |
|---------|---------|-----|
| `rxdart` | ^0.22.6 | Streams reactivos para BLoCs |
| `provider` | ^4.0.4 | Root widget Provider |

### Red y comunicación
| Paquete | Versión | Uso |
|---------|---------|-----|
| `http` | ^0.12.1 | Llamadas REST al backend |
| `socket_io_client` | ^0.9.8 | Conexión Socket.IO tiempo real |
| `connectivity` | ^3.0.3 | Verificar red antes de requests |
| `url_launcher` | ^5.4.10 | Abrir URLs en browser externo |
| `mime` | ^0.9.6+3 | Detección MIME para upload de archivos |
| `http_parser` | ^3.1.3 | Parsing de headers HTTP |
| `android_intent` | ^0.3.5+1 | Intents Android nativos |

### Firebase
| Paquete | Versión | Uso |
|---------|---------|-----|
| `firebase_messaging` | ^6.0.12 | Push notifications (FCM) |
| `firebase_analytics` | ^5.0.11 | Tracking de eventos y pantallas |

### Geolocalización y mapas
| Paquete | Versión | Uso |
|---------|---------|-----|
| `geolocator` | ^7.0.1 | GPS del dispositivo |
| `flutter_map` | ^0.10.1+1 | Mapa interactivo (MapBox/Leaflet) |
| `latlong` | ^0.6.1 | Tipos de coordenadas |

### Almacenamiento
| Paquete | Versión | Uso |
|---------|---------|-----|
| `shared_preferences` | ^0.5.6+3 | Persistencia local key-value |
| `path_provider` | ^1.6.7 | Rutas del sistema de archivos |

### Archivos y documentos
| Paquete | Versión | Uso |
|---------|---------|-----|
| `barcode_scan` | ^1.0.0 | Escaneo de código de barras/QR |
| `barcode_flutter` | ^1.1.0 | Generación de barcodes |
| `pdf` | ^1.4.1 | Generación de documentos PDF |
| `image_picker` | ^0.6.6+5 | Selección de imágenes/archivos |

### Utilidades
| Paquete | Versión | Uso |
|---------|---------|-----|
| `intl` | ^0.16.1 | Internacionalización y formateo de fechas |
| `validators` | ^2.0.0+1 | Validación de strings (email, etc.) |
| `transparent_image` | ^1.0.0 | Imagen placeholder transparente |

## Dependencias de desarrollo

| Paquete | Versión | Uso |
|---------|---------|-----|
| `flutter_test` | SDK | Testing |
| `flutter_lints` | — | Reglas de lint |

## Servicios externos

| Servicio | URL / Token | Propósito |
|---------|-------------|-----------|
| API Backend | `https://panel.muvinapp.com/api/backend/web/` | REST principal |
| Socket.IO | `https://sockets.muvinapp.com` | Tiempo real |
| Firebase FCM | `fcm.googleapis.com/fcm/send` | Push notifications |
| MapBox | `pk.eyJ1IjoiYWxmb25zb21jIi...` | Mapas |

---

Ver también: [[vision-general]] · [[arquitectura-alto-nivel]] · [[riesgos]]
