# Requisitos de Entorno y Configuración — app-clients

> [[README]]

## Requisitos de desarrollo

| Herramienta | Versión requerida | Notas |
|-------------|-------------------|-------|
| Flutter SDK | `>=2.0.0 <3.0.0` | ⚠️ Pre-null-safety. Flutter 3.x rompe dependencias legacy |
| Dart SDK | `>=2.1.0 <3.0.0` | Incluido con Flutter |
| Android Studio / VS Code | Cualquiera reciente | Con plugin Flutter/Dart |
| Xcode | ≥13 (para iOS) | Solo macOS |
| Android SDK | API 21+ (minSdk) | |
| Java | 11+ | Para build Android |
| Node.js | — | No requerido directamente |

## Configuración de Firebase

1. Crear proyecto en [Firebase Console](https://console.firebase.google.com).
2. Registrar app Android con package name `com.muvinapp.clientes` (verificar en `AndroidManifest.xml`).
3. Descargar `google-services.json` → colocar en `android/app/`.
4. Registrar app iOS → descargar `GoogleService-Info.plist` → colocar en `ios/Runner/`.
5. Habilitar servicios: **Analytics**, **Cloud Messaging**.

## Variables de entorno / credenciales

> 🔴 Actualmente todo está hardcodeado en `config.dart`. El proceso ideal es usar `--dart-define`:

```bash
flutter build apk \
  --dart-define=API_URL=https://panel.muvinapp.com/api/backend/web/ \
  --dart-define=X_API_KEY=TU_CLAVE \
  --dart-define=WS_URL=socket.muvinapp.com
```

Y en `config.dart`:
```dart
static const String apiUrl = String.fromEnvironment('API_URL');
static const String xApiKey = String.fromEnvironment('X_API_KEY');
```

## Comandos de desarrollo

```bash
# Instalar dependencias
flutter pub get

# Ejecutar en debug (dispositivo/emulador conectado)
flutter run

# Build APK release
flutter build apk --release

# Build IPA (solo macOS)
flutter build ios --release

# Ejecutar tests
flutter test
```

## Orientación de pantalla

La app está configurada para **portrait only** en `main.dart`:
```dart
SystemChrome.setPreferredOrientations([DeviceOrientation.portraitUp]);
```
