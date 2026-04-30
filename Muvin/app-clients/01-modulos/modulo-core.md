# Módulo: Core (Infraestructura)

> **Ruta/Namespace:** `lib/src/providers/`, `lib/src/config/`, `lib/src/utils/`, `lib/src/share/`
> **Criticidad:** 🔴 Crítica
> **Estado:** Activo

## Propósito

Capa de infraestructura transversal. Contiene el cliente HTTP (`MuvinProvider`), la configuración de entorno (`Config`), las utilidades de persistencia local (`Preference`), y helpers compartidos (`share/`). Todo el acceso a la API y al almacenamiento local pasa por este módulo.

## Componentes principales

| Componente | Archivo | Responsabilidad |
|-----------|---------|-----------------|
| `MuvinProvider` | `providers/muvin_provider.dart` | Cliente HTTP; todos los ~30 endpoints de la API |
| `Config` | `config/config.dart` | Constantes de entorno (URL, tokens) |
| `Preference` | `config/preference.dart` | Acceso a `SharedPreferences` (auth state) |
| `Utils / Helpers` | `share/` | Funciones de formato, validación, fechas |

## Config

```dart
class Config {
  static const String apiUrl      = 'https://panel.muvinapp.com/api/backend/web/';
  static const String xApiKey     = 'uH9hatw8G3lQls7fE3';   // 🔴 hardcodeado
  static const String serverToken = '...';                    // 🔴 FCM server token hardcodeado
  static const String urlWebSocket = 'socket.muvinapp.com';
}
```

> 🔴 **RIESGO CRÍTICO:** Todas las credenciales están hardcodeadas en código fuente. Ver [security-inventory](../05-inventarios/security-inventory.md).

## Preference (SharedPreferences)

Claves persistidas en disco por `Preference`:

| Clave | Tipo | Descripción |
|-------|------|-------------|
| `access_token` | String | JWT de acceso |
| `refresh_token` | String | Token de refresco |
| `userId` | String | ID del usuario |
| `userName` | String | Nombre visible |
| `userEmail` | String | Email |
| `esDadorCupo` | bool | Perfil admin/dador |
| `esClienteFinal` | bool | Perfil cliente final |
| `fcmToken` | String | Token FCM del dispositivo |

## MuvinProvider — Estructura de llamada HTTP

```dart
Future<Map> _get(String path, {Map<String,String>? params}) async {
  final token = await Preference.getToken();
  final response = await http.get(
    Uri.parse(Config.apiUrl + path).replace(queryParameters: params),
    headers: {
      'X-Api-Key': Config.xApiKey,
      'Authorization': 'Bearer $token',
    }
  );
  return json.decode(response.body);
}
```

Todas las llamadas agregan automáticamente `X-Api-Key` y `Authorization`. Si el token expira, **no hay interceptor de refresh automático** — el usuario debe hacer login nuevamente.

## Dependencias externas

- `http ^0.12.x` — cliente HTTP puro (no Dio)
- `shared_preferences ^0.5.x` — persistencia clave-valor
- `socket_io_client ^0.9.x` — WebSocket
- `firebase_messaging ^6.x` — notificaciones push

## Riesgos y deuda técnica

- 🔴 `Config.xApiKey` y `Config.serverToken` en código fuente → exposición en repositorio y en APK descompilado.
- 🔴 No hay interceptor de token refresh. Token expirado = sesión rota silenciosamente.
- ⚠️ `http ^0.12.x` es versión muy antigua. No soporta cancelación de requests.
- ⚠️ `SharedPreferences` guarda el `access_token` sin cifrado. En dispositivos rooteados es accesible.
- ⚠️ No existe capa de `Repository`; los BLoCs llaman directamente a `MuvinProvider`. Dificulta testing.

## Archivos fuente relevantes

- `lib/src/providers/muvin_provider.dart`
- `lib/src/config/config.dart`
- `lib/src/config/preference.dart`
- `lib/src/share/` (helpers)
- `lib/main.dart` (inicialización Socket + FCM)
