# Índice de Módulos — app-drivers

> [[../README]]

## Módulos del sistema

| # | Módulo | Archivo(s) | Descripción | Criticidad |
|---|--------|------------|-------------|-----------|
| 1 | SplashScreen | `pages/splashscreen_page.dart` | Arranque: verifica token y permisos | 🔴 Alta |
| 2 | Auth (Login + Verificación) | `pages/login_page.dart`, `pages/phoneverification_page.dart` | Login por teléfono + SMS | 🔴 Alta |
| 3 | Home | `pages/home_page.dart` | Dashboard + tracking GPS | 🔴 Alta |
| 4 | Cargas | `pages/cargas_page.dart`, `pages/cargas_detalle_page.dart` | Listado y detalle de pedidos públicos | 🔴 Alta |
| 5 | Postulaciones | `pages/postulaciones_page.dart` | Gestión de postulaciones del chofer | 🟠 Media |
| 6 | Viaje | `pages/viaje_page.dart` | Detalle del viaje asignado | 🟠 Media |
| 7 | Carta de Porte | `pages/carta_porte_page.dart` | 🔴 Rota — nunca funcional | 🔴 Crítica |
| 8 | Signup | `pages/signup_page.dart` | 💀 Vacía — Container() | 💀 Muerta |
| 9 | Intro | `pages/intro_page.dart` | Onboarding ⚠️ Pendiente de verificar | 🟡 Baja |
| 10 | MuvinProvider | `providers/muvin_provider.dart` | Cliente HTTP REST (todos los endpoints) | 🔴 Alta |
| 11 | GeolocatorProvider | `providers/geolocator_provider.dart` | Proveedor de posición GPS | 🟠 Media |
| 12 | BLoC / Provider | `blocs/provider.dart`, `blocs/register_bloc.dart`, `blocs/phonecode_bloc.dart` | Estado del formulario de login | 🟡 Baja |
| 13 | Preferences | `share/preference.dart` | Singleton SharedPreferences (token) | 🔴 Alta |
| 14 | Config | `config/config.dart` | URLs y tokens de configuración | 🔴 Alta (hardcoded) |
| 15 | Theme / Utils | `theme/style.dart`, `utils/utils.dart` | Estilos y helpers de UI | 🟡 Baja |

## Detalle

- [[modulo-splashscreen]]
- [[modulo-auth]]
- [[modulo-home]]
- [[modulo-cargas]]
- [[modulo-postulaciones]]
- [[modulo-viaje]]
- [[modulo-carta-porte]]
- [[modulo-muvin-provider]]
- [[modulo-blocs]]
