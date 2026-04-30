# Recomendaciones de Modernización — app-clients

> [[README]] · [[deuda-tecnica]] · [[hotspots]]

## Fase 1: Seguridad inmediata (1-2 semanas)

### 1.1 Eliminar credenciales del código fuente
```bash
# Usar dart-define en builds
flutter build apk \
  --dart-define=API_URL=https://panel.muvinapp.com/api/backend/web/ \
  --dart-define=X_API_KEY=NUEVA_CLAVE_ROTADA \
  --dart-define=WS_URL=wss://socket.muvinapp.com
```
```dart
// config.dart
static const String apiUrl = String.fromEnvironment('API_URL', defaultValue: '');
static const String xApiKey = String.fromEnvironment('X_API_KEY', defaultValue: '');
```
- **Rotar la X-Api-Key** actual (`uH9hatw8G3lQls7fE3`) — puede estar comprometida.
- **Eliminar `serverToken` FCM del cliente.** Las notificaciones push deben enviarse exclusivamente desde el backend.

### 1.2 Cifrar tokens en SharedPreferences
Reemplazar `shared_preferences` por `flutter_secure_storage` para `access_token` y `refresh_token`:
```dart
final storage = FlutterSecureStorage();
await storage.write(key: 'access_token', value: token);
```

### 1.3 Implementar refresh de token
Añadir interceptor en `MuvinProvider`:
```dart
if (response.statusCode == 401) {
  final refreshed = await _refreshToken();
  if (refreshed) return _retry(request);
  else _navigateToLogin();
}
```

---

## Fase 2: Estabilidad de dependencias (2-4 semanas)

### 2.1 Migración a null-safety (Dart 2.12+)
```bash
dart migrate --apply-changes
```
Requiere: actualizar todas las dependencias a versiones null-safe primero.

### 2.2 Actualizar Firebase a FlutterFire unificado
```yaml
firebase_core: ^2.x
firebase_analytics: ^10.x
firebase_messaging: ^14.x
```

### 2.3 Actualizar RxDart y Socket.IO
```yaml
rxdart: ^0.27.x
socket_io_client: ^2.x
```

---

## Fase 3: Arquitectura (1-2 meses)

### 3.1 Separar MuvinProvider por dominio
```
providers/
  auth_api_service.dart
  cupos_api_service.dart
  cargas_api_service.dart
  catalogos_api_service.dart
```

### 3.2 Agregar capa Repository
```
BLoC → Repository → ApiService
```
Permite mockear en tests sin depender de HTTP real.

### 3.3 Implementar transaccionalidad en Cargas
Opciones:
- Opción A: Unificar `POST pedido` + `PUT cupo` en un único endpoint del backend.
- Opción B: Implementar saga en el cliente con rollback explícito si `PUT cupo` falla.

### 3.4 Migrar a Flutter 3.x
Una vez completadas las fases 1 y 2, actualizar:
```yaml
environment:
  sdk: ">=3.0.0 <4.0.0"
  flutter: ">=3.16.0"
```

---

## Métricas de éxito

| Objetivo | Métrica |
|----------|---------|
| Cero credenciales en código | `grep -r "uH9hatw8" lib/` → sin resultados |
| Token seguro | Uso de `flutter_secure_storage` verificado |
| Dependencias actualizadas | `flutter pub outdated` → 0 paquetes legacy |
| Cobertura de tests | ≥60% en BLoCs críticos |
| Null-safety | `dart analyze` → 0 warnings de null-safety |
