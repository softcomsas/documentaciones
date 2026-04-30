# Configuración y Despliegue — app-clients

> [[README]] · [[requisitos-entorno]]

## Environments

La app **no tiene sistema de environments** (dev/staging/prod). Toda la configuración apunta siempre a producción:

```dart
// lib/src/config/config.dart
static const String apiUrl = 'https://panel.muvinapp.com/api/backend/web/';
static const String urlWebSocket = 'socket.muvinapp.com';
```

> ⚠️ Para testing se debe modificar manualmente `config.dart`. No existe un mecanismo de `--flavor` o `--dart-define` implementado.

## Pipeline de build (manual — sin CI/CD documentado)

```
1. flutter pub get
2. flutter build apk --release   (Android)
3. flutter build ios --release   (iOS — requiere macOS + certificados)
4. Subir a Play Store / App Store manualmente
```

## Configuración de signing Android

El `keystore` de release debe configurarse en `android/app/build.gradle`:
```groovy
signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile file(keystoreProperties['storeFile'])
        storePassword keystoreProperties['storePassword']
    }
}
```
Las propiedades deben estar en `android/key.properties` (no commitear al repositorio).

## Configuración de versión

En `pubspec.yaml`:
```yaml
name: muvinapp_clientes
version: 1.0.0+2  # nombre+buildNumber
```

Para releases incrementar tanto `version` como el build number (`+N`).

## Dockerfile

No se encontró `Dockerfile` en el repositorio (es una app móvil nativa, no una app web). El despliegue es directo a tiendas de apps.

## Monitoreo en producción

- **Firebase Analytics:** eventos de navegación y acciones clave.
- **Firebase Crashlytics:** no confirmado — verificar si está configurado en `pubspec.yaml`.
- **No hay APM** (Application Performance Monitoring) documentado.
