# Inventario de Seguridad

> **Sección:** 05-inventarios
> **Proyecto:** app-drivers

## Hallazgos Críticos

### 🔴 Credenciales hardcodeadas en el repositorio

| Item | Archivo | Valor |
|------|---------|-------|
| API URL (test) | `config.dart` | `https://pruebas.muvinapp.com/api/backend/web/` |
| MapBox token | `config.dart` | `pk.eyJ1IjoiYWxmb25zb21jIiwiYSI6ImNrNDJ4ZWF5ZjAxNmIzbW54cTdnbW44ZWMifQ.-ulQpVqM0w36mJc_djcSgg` |
| apiKey | `config.dart` | `'api_key'` (placeholder sin reemplazar) |

**Impacto:** Cualquier persona con acceso al repositorio puede usar el token de MapBox (costos) y acceder al endpoint de pruebas.

### 🔴 Token de sesión en texto plano

El token Bearer se guarda en `SharedPreferences` sin cifrado. En dispositivos rooteados o con backup de Android, el token es legible directamente.

### ⚠️ Sin verificación de certificados SSL

El paquete `http ^0.12.1` no fuerza certificate pinning. Posible ataque MITM en redes no confiables.

### ⚠️ Sin guards de autenticación en rutas

Las rutas nombradas no tienen middleware de autenticación. Si el token expira o es inválido, el backend devuelve 401 pero la app no redirige al login.

### ⚠️ Código de país hardcodeado

```dart
bloc.changeCountry('1'); // Hardcodeado a +1 (EE.UU.)
```
Impide el uso de la app con números de otros países.

### ⚠️ Valores de configuración sin usar (legacy)

```dart
// config.dart
static const language = 'vi';  // Vietnamese
static const region = 'VN';    // Vietnam
```
Sugieren que el proyecto fue derivado de otro proyecto para Vietnam y estos valores nunca se limpiaron.

## Recomendaciones

1. **Mover credenciales** a variables de entorno (`.env` con `flutter_dotenv`).
2. **Cifrar el token** con `flutter_secure_storage`.
3. **Implementar auth guard** que detecte 401 y redirija al login.
4. **Certificate pinning** para la URL de producción.
5. **Rotar el token de MapBox** inmediatamente — está comprometido en el repo.
