# Operación y despliegue — app-shifts

## Requisitos previos

- Flutter SDK 1.x (canal stable, versión compatible con Dart <3.0.0)
- Android SDK (API 21+ recomendado)
- Archivo `google-services.json` en `android/app/` (Firebase)
- Conexión a internet para las dependencias de pub.dev

## Build Android

```powershell
# Instalar dependencias
flutter pub get

# Build APK debug
flutter build apk --debug

# Build APK release
flutter build apk --release

# Build AppBundle (Play Store)
flutter build appbundle --release
```

## Configuración de entorno

Todos los parámetros de entorno están hardcodeados en `lib/src/config/config.dart`. **No existe sistema de variables de entorno ni flavors configurados**.

Para cambiar el entorno (ej: apuntar a staging en lugar de producción) se debe editar manualmente:

```dart
// lib/src/config/config.dart
static const String apiUrl = "https://panel.muvinapp.com/api/backend/web/";  // PRODUCCIÓN
static const String urlWebSocket = "https://sockets.muvinapp.com";           // PRODUCCIÓN
```

> ⚠️ **No existe configuración para ambiente de pruebas / staging.** Ver [[riesgos]].

## Firebase

1. Crear proyecto en [Firebase Console](https://console.firebase.google.com/)
2. Registrar la app Android con package: `com.muvinapp.muvin_app_choferes`
3. Descargar `google-services.json` y colocarlo en `android/app/`
4. El `serverToken` de FCM se actualiza en `config.dart` → `Config.serverToken`

## Permisos Android (AndroidManifest.xml)

Los siguientes permisos son necesarios para el correcto funcionamiento:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```

## Firma de release

Para publicar en Play Store se necesita un keystore. Configurar en `android/app/build.gradle`:

```gradle
signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile file(keystoreProperties['storeFile'])
        storePassword keystoreProperties['storePassword']
    }
}
```

## Docker

El repositorio incluye un `Dockerfile` en la raíz para build en CI/CD. No se incluyen instrucciones adicionales de configuración Docker en el README original.

## Problemas conocidos en build

- Flutter 1.x puede requerir versiones específicas de Gradle y Android Gradle Plugin
- `barcode_scan: ^1.0.0` requiere configuración adicional de permisos de cámara en Android
- `geolocator: ^7.0.1` con Flutter 1.x puede tener incompatibilidades con versiones modernas de Gradle

---

Ver también: [[inventario-general]] · [[riesgos]] · [[stack-tecnologico]]
