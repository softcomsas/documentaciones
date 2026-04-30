# Core vs Dependencias Personalizadas — app-clients

> [[README]] · [[depends-matrix]]

## Flutter/Dart Core (sin dependencia externa)

| Componente | Origen |
|-----------|--------|
| `StatefulWidget` / `StatelessWidget` | Flutter SDK |
| `InheritedWidget` (`BlocProvider`) | Flutter SDK |
| `Navigator` / rutas nombradas | Flutter SDK |
| `StreamBuilder` | Dart SDK |
| `Future` / `async-await` | Dart SDK |
| `jsonDecode` / `jsonEncode` | `dart:convert` |
| `Uri` | `dart:core` |

## Dependencias de terceros (pub.dev)

| Paquete | Justificación de uso | Alternativa moderna |
|---------|---------------------|---------------------|
| `rxdart` | Streams reactivos con BehaviorSubject | `rxdart ^0.27+` (null-safe) |
| `http` | HTTP client | `http ^1.x` o `dio` |
| `shared_preferences` | Persistencia clave-valor | `shared_preferences ^2.x` |
| `socket_io_client` | WebSocket tiempo real | `socket_io_client ^2.x` |
| `firebase_core` | Firebase init | `firebase_core ^2.x` (FlutterFire) |
| `firebase_analytics` | Tracking | `firebase_analytics ^10.x` |
| `firebase_messaging` | Push | `firebase_messaging ^14.x` |
| `geolocator` | GPS | `geolocator ^10.x` |
| `flutter_local_notifications` | Notifs locales | `^16.x` |
| `intl` | Fechas/formatos | `^0.18+` |

## Código 100% custom (sin paquete)

| Componente | Descripción |
|-----------|-------------|
| `BlocProvider` | InheritedWidget propio, no usa `provider` package |
| `MuvinProvider` | HTTP client wrapper propio sobre `http` |
| `Preference` | Wrapper sobre `shared_preferences` |
| `Config` | Singleton de constantes de entorno |
| Todos los BLoCs | Patrón propio con RxDart |
