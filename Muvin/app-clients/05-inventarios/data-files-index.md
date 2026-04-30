# Índice de Archivos de Datos — app-clients

> [[README]] · [[tree-estructura-archivos]]

## Archivos de configuración y datos estáticos

| Archivo | Tipo | Descripción |
|---------|------|-------------|
| `pubspec.yaml` | YAML | Dependencias, nombre, versión del paquete Flutter |
| `pubspec.lock` | YAML | Lock de versiones exactas |
| `lib/src/config/config.dart` | Dart/const | Constantes de entorno (URL, keys) |
| `android/app/google-services.json` | JSON | Configuración Firebase Android |
| `ios/Runner/GoogleService-Info.plist` | Plist | Configuración Firebase iOS |

## Archivos de assets declarados en pubspec.yaml

| Path | Tipo | Uso |
|------|------|-----|
| `assets/images/` | PNG/JPG | Logos, imágenes de la app |
| `assets/icons/` | PNG | Iconos personalizados |

## Archivos de build/plataforma

| Archivo | Plataforma | Descripción |
|---------|-----------|-------------|
| `android/app/build.gradle` | Android | Config de build, keystore, minSdk |
| `ios/Runner/Info.plist` | iOS | Permisos (location, camera, notifications) |
| `android/app/src/main/AndroidManifest.xml` | Android | Permisos y activities |

## Permisos declarados (conocidos)

| Permiso | Plataforma | Uso |
|---------|-----------|-----|
| `ACCESS_FINE_LOCATION` | Android | Geolocator |
| `ACCESS_COARSE_LOCATION` | Android | Geolocator |
| `RECEIVE_BOOT_COMPLETED` | Android | Notificaciones locales |
| `VIBRATE` | Android | Notificaciones |
| `NSLocationWhenInUseUsageDescription` | iOS | Geolocator |
| `NSLocationAlwaysUsageDescription` | iOS | Geolocator background |
