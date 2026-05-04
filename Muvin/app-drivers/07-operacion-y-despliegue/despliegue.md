# Operación y Despliegue

> **Sección:** 07-operacion-y-despliegue
> **Proyecto:** app-drivers
> **Framework:** Flutter 1.x | Dart >=2.1.0 <3.0.0

## ⚠️ Advertencia

Esta app usa Flutter 1.x. La instalación de dependencias puede fallar con versiones modernas del SDK de Flutter. Se recomienda usar Flutter 1.22.x o 1.17.x para compilar.

---

## Requisitos de entorno

| Herramienta | Versión recomendada |
|-------------|---------------------|
| Flutter SDK | 1.17.x – 1.22.x |
| Dart SDK | >=2.1.0 <3.0.0 (incluido con Flutter) |
| Android SDK | API 21+ (Android 5.0+) |
| Xcode | 11.x (para iOS) |
| Java JDK | 8 o 11 |

---

## Instalación local

```powershell
# 1. Clonar el repositorio
git clone <repo-url>
cd app-drivers

# 2. Obtener dependencias
flutter pub get

# 3. Verificar entorno
flutter doctor

# 4. Ejecutar en dispositivo/emulador
flutter run
```

---

## Build de producción

```powershell
# Android APK
flutter build apk --release

# Android App Bundle (Play Store)
flutter build appbundle --release

# iOS (requiere Mac + Xcode)
flutter build ios --release
```

Los artefactos de salida:
- APK: `build/app/outputs/flutter-apk/app-release.apk`
- AAB: `build/app/outputs/bundle/release/app-release.aab`

---

## Configuración de entorno

> 🔴 **No existe sistema de entornos.** Toda la configuración está hardcodeada en `lib/src/config/config.dart`.

Para cambiar la URL de la API, editar manualmente:

```dart
// lib/src/config/config.dart
class Config {
  static const apiUrl = 'https://TU-URL-PRODUCCION.com/api/backend/web/';
  static const tokenMapBox = 'TU-TOKEN-MAPBOX';
}
```

**Recomendación:** Migrar a `flutter_dotenv` o `--dart-define` para gestión de entornos:

```powershell
# Build con configuración de producción via dart-define
flutter build apk --dart-define=API_URL=https://prod.muvinapp.com/api/backend/web/
```

---

## Permisos requeridos

### Android (`android/app/src/main/AndroidManifest.xml`)

| Permiso | Uso |
|---------|-----|
| `ACCESS_FINE_LOCATION` | GPS tracking |
| `ACCESS_COARSE_LOCATION` | GPS fallback |
| `CAMERA` | Foto de remitos |
| `INTERNET` | Todas las llamadas HTTP |
| `READ_EXTERNAL_STORAGE` | Selección de imagen |

### iOS (`ios/Runner/Info.plist`)

| Key | Uso |
|-----|-----|
| `NSLocationWhenInUseUsageDescription` | GPS tracking |
| `NSCameraUsageDescription` | Foto remitos |
| `NSPhotoLibraryUsageDescription` | Galería |

---

## Variables de entorno actuales (hardcodeadas)

| Variable | Valor actual | Entorno |
|----------|-------------|---------|
| `apiUrl` | `https://pruebas.muvinapp.com/api/backend/web/` | 🔴 **PRUEBAS** |
| `tokenMapBox` | `pk.eyJ1IjoiYWxmb25zb...` | Desconocido |
