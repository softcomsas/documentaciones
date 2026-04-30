# Matriz de Dependencias de Paquetes — app-clients

> [[README]] · [[tree-estructura-archivos]]

## Dependencias de producción (`pubspec.yaml`)

| Paquete | Versión | Uso | Estado |
|---------|---------|-----|--------|
| `flutter` | SDK | Framework base | ✅ |
| `rxdart` | ^0.23.x | BLoC streams/subjects | 🔴 Legacy — API cambió en ^0.27 |
| `http` | ^0.12.x | Cliente HTTP | 🔴 Legacy — sin cancelación de requests |
| `shared_preferences` | ^0.5.x | Persistencia local | ⚠️ Actualizable a ^2.x |
| `socket_io_client` | ^0.9.x | WebSocket | 🔴 Legacy — API cambió en ^2.x |
| `firebase_core` | ^0.4.x | Init Firebase | 🔴 Pre-FlutterFire unificado |
| `firebase_analytics` | ^5.x | Analytics eventos | 🔴 Pre-FlutterFire unificado |
| `firebase_messaging` | ^6.x | Push notifications | 🔴 Pre-FlutterFire unificado |
| `geolocator` | ^5.x | Geolocalización | ⚠️ Actualizable a ^10.x |
| `flutter_local_notifications` | ^1.x | Notificaciones locales | ⚠️ Actualizable a ^16.x |
| `intl` | ^0.16.x | Formateo fechas/números | ⚠️ Actualizable |
| `provider` | — | No confirmado (BLoC propio) | — |

## Dependencias de desarrollo

| Paquete | Uso |
|---------|-----|
| `flutter_test` | Tests unitarios |
| `mockito` | Mocking en tests |

## Resumen de estado

| Estado | Cantidad | Impacto |
|--------|---------|---------|
| ✅ OK | ~18 | — |
| ⚠️ Actualizable | ~4 | Bajo |
| 🔴 Legacy/Deprecado | 6 | Alto — requieren migración |

## Riesgo principal

Las 6 dependencias 🔴 son incompatibles con Flutter 3.x / Dart 3.x (null-safety). Para actualizar el SDK de Flutter se requiere una migración significativa del código.
