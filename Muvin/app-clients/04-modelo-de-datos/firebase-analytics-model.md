# FirebaseAnalyticsModel

> **Archivo fuente:** `lib/src/models/firebase_analytics_model.dart`

## Descripción

Wrapper para eventos de Firebase Analytics. Encapsula el nombre del evento y sus parámetros, permitiendo tipar los eventos que se registran en la app.

## Campos

| Campo | Tipo Dart | Descripción |
|-------|-----------|-------------|
| `eventName` | `String` | Nombre del evento (ej. `login`, `view_cupos`) |
| `params` | `Map<String, dynamic>?` | Parámetros adicionales del evento |

## Eventos registrados (conocidos)

| Evento | Parámetros | Cuándo |
|--------|-----------|--------|
| `login` | `{userId}` | Login exitoso |
| `view_cupos` | `{perfil}` | Ingreso a pantalla de cupos |
| `crear_pedido` | `{cupoId, cantidad}` | Pedido creado exitosamente |
| `logout` | — | Cierre de sesión |

## Notas

- `firebase_analytics ^5.x` es versión legacy. Actualizar a `^10.x` con FlutterFire unificado.
