# Riesgos y deuda técnica — app-shifts

## 🔴 CRÍTICO — FCM Server Token hardcodeado

**Archivo**: `lib/src/config/config.dart`

```dart
static const String serverToken = "AAAAZF2L6K4:APA91bHhp9sUhjm7iIvdX5btIetbXa4TKjAuVs61G0eg...";
```

**Impacto**: El `serverToken` de Firebase Cloud Messaging es una **clave privada del servidor** que permite enviar notificaciones push a **cualquier usuario de la aplicación**. Exponer esta clave en el código fuente significa que cualquier persona con acceso al repositorio puede:
- Enviar notificaciones push a todos los choferes
- Suplantar al servidor para enviar mensajes maliciosos
- Agotar la cuota de FCM de la cuenta de Firebase

**Acción requerida**:
1. Revocar el serverToken actual en la consola de Firebase
2. Nunca incluir el serverToken en la app cliente — debe estar **solo en el backend**
3. Utilizar el backend para enviar notificaciones, no la app directamente

---

## 🔴 ALTO — xApiKey hardcodeado

**Archivo**: `lib/src/config/config.dart`

```dart
static const xApiKey = 'uH9hatw8G3lQls7fE3';
```

**Impacto**: Esta API key se envía como header `X-Api-Key` en todas las peticiones autenticadas. Al estar en el código fuente, cualquiera puede hacer peticiones al backend haciéndose pasar por la app.

**Acción requerida**: Rotar la clave y explorar mecanismos de certificate pinning o generación dinámica de tokens.

---

## 🔴 ALTO — MapBox token hardcodeado

**Archivo**: `lib/src/config/config.dart`

```dart
static const String tokenMapBox = 'pk.eyJ1IjoiYWxmb25zb21jIiwiYSI6ImNrNDJ4ZWF5ZjAxNmIzbW54cTdnbW44ZWMifQ.-ulQpVqM0w36mJc_djcSgg';
```

**Impacto**: Token público de MapBox asociado al usuario `alfonsomC`. Si se abusa, los costos se cargan al propietario del token. Se puede restringir el token por dominio/bundle desde el dashboard de MapBox.

**Acción requerida**: Restringir el token por `bundleId` desde el dashboard de MapBox (`com.muvinapp.muvin_app_choferes`).

---

## 🟡 MEDIO — Flutter 1.x / Dart sin null safety (EOL)

**Versión**: Flutter 1.x, Dart ≥2.1.0 <3.0.0

**Impacto**:
- Flutter 1.x ya no recibe actualizaciones de seguridad ni parches
- Sin null safety: riesgo de NullPointerException en runtime difícil de detectar en compilación
- Incompatible con versiones modernas de paquetes (la mayoría requieren Dart ≥2.12)
- Dificultad para onboarding de nuevos desarrolladores con Flutter 3.x+

**Esfuerzo de migración**: Alto — requiere migración a null safety + actualización de todas las dependencias (rxdart, firebase_messaging, geolocator, etc. tienen breaking changes entre versiones).

---

## 🟡 MEDIO — Dependencias mayores obsoletas

| Paquete | Versión usada | Versión actual | Notas |
|---------|--------------|----------------|-------|
| `firebase_messaging` | ^6.0.12 | ^14.x | API completamente reescrita |
| `firebase_analytics` | ^5.0.11 | ^10.x | Breaking changes |
| `geolocator` | ^7.0.1 | ^11.x | API de permisos cambiada |
| `rxdart` | ^0.22.6 | ^0.27.x | Observable eliminado, usar Stream |
| `socket_io_client` | ^0.9.8 | ^2.x | Breaking changes |
| `image_picker` | ^0.6.6+5 | ^1.x | Breaking changes |
| `barcode_scan` | ^1.0.0 | Archivado | Usar `mobile_scanner` |
| `connectivity` | ^3.0.3 | Archivado | Usar `connectivity_plus` |

---

## 🟡 MEDIO — URL de producción hardcodeada (sin ambiente de staging)

```dart
static const String apiUrl = "https://panel.muvinapp.com/api/backend/web/";
```

**Impacto**: No existe separación de ambientes (dev/staging/prod). Cualquier build de debug apunta directamente a producción, lo que puede contaminar datos reales durante el desarrollo.

**Acción requerida**: Implementar `flutter_dotenv` o `--dart-define` para gestionar variables por ambiente.

---

## 🟡 MEDIO — Password generada con lógica predecible

```dart
"password": "P@ssW${bloc.cuit}"
```

El password del chofer se genera automáticamente como `P@ssW` + su CUIT. Esto implica:
- El CUIT es dato público en Argentina
- Cualquiera que conozca el CUIT de un chofer podría calcular su contraseña

**Acción requerida**: Implementar generación de contraseña con componente aleatorio o eliminar el campo de contraseña si la autenticación es exclusivamente por SMS.

---

## 🟢 BAJO — Typo en nombre de clase

```dart
class BenefioPage extends StatefulWidget  // falta 'ci'
```

Afecta la ruta `/beneficios`. No causa errores funcionales pero es deuda de calidad de código.

---

## 🟢 BAJO — Código legacy de Vietnam

```dart
static const String language = 'vi';  // Vietnam
static const String region = 'VN';
```

Vestigio de cuando la plataforma se usaba en otro mercado. No afecta funcionalidad si no se usa activamente.

---

## 🟢 BAJO — `print()` statements en producción

El `MuvinProvider` y otros archivos contienen múltiples `print()` con datos sensibles:
```dart
print(myData);         // puede exponer datos del usuario en logs
print(dataHeader);     // puede exponer el Bearer token en logs
```

**Acción requerida**: Reemplazar con logger condicional (`kDebugMode`) o usar un paquete de logging.

---

## Resumen de prioridades

| Prioridad | Item | Esfuerzo |
|-----------|------|---------|
| 🔴 P1 | Revocar FCM serverToken y moverlo al backend | Bajo |
| 🔴 P1 | Rotar xApiKey | Bajo |
| 🔴 P2 | Restringir MapBox token por bundleId | Bajo |
| 🟡 P3 | Implementar ambientes (dev/staging/prod) | Medio |
| 🟡 P4 | Migrar a Flutter 3.x + null safety | Alto |
| 🟡 P5 | Actualizar dependencias obsoletas | Alto |
| 🟡 P6 | Revisar lógica de generación de passwords | Medio |
| 🟢 P7 | Eliminar print() con datos sensibles | Bajo |
| 🟢 P8 | Corregir typo BenefioPage | Mínimo |

---

Ver también: [[inventario-general]] · [[stack-tecnologico]] · [[build-flutter]]
