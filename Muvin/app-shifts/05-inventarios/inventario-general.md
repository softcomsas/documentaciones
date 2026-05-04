# Inventario general — app-shifts

## Árbol de archivos principal

```
lib/
├── main.dart                          # Bootstrap: Preferences, Provider root, SplashScreen
└── src/
    ├── config/
    │   └── config.dart                # 🔴 Todas las credenciales hardcodeadas
    ├── routes/
    │   └── routes.dart                # 28 rutas nombradas + FadePageRoute
    ├── pages/                         # 26 páginas UI
    │   ├── splashscreen_page.dart
    │   ├── home_page.dart
    │   ├── login_page.dart
    │   ├── phone_verification_page.dart
    │   ├── signup_page.dart
    │   ├── intro_page.dart
    │   ├── cargas_page.dart
    │   ├── cargas_detalle_page.dart
    │   ├── postulaciones_page.dart
    │   ├── viaje_page.dart
    │   ├── carta_porte_page.dart
    │   ├── crear_usuario_page.dart
    │   ├── usuario_page.dart
    │   ├── turno_page.dart            # Funcionalidad core: turnos de puerto
    │   ├── calada_page.dart
    │   ├── actualizar_usuario_page.dart
    │   ├── consultas_page.dart
    │   ├── compartir_page.dart
    │   ├── novedades_page.dart
    │   ├── novedades_detalle_page.dart
    │   ├── seguros_page.dart
    │   ├── beneficios_page.dart       # clase: BenefioPage (typo)
    │   ├── filtro_page.dart
    │   ├── verificar_cuit_page.dart
    │   └── verificar_codigo_email_page.dart
    ├── blocs/                         # 13 BLoCs RxDart
    │   ├── provider.dart              # InheritedWidget que expone todos los BLoCs
    │   ├── register_bloc.dart
    │   ├── phonecode_bloc.dart
    │   ├── usuario_bloc.dart
    │   ├── turno_bloc.dart
    │   ├── calada_bloc.dart
    │   ├── cartaporte_bloc.dart
    │   ├── compartir_bloc.dart
    │   ├── consulta_bloc.dart
    │   ├── denuncia_siniestro_bloc.dart
    │   ├── filtro_bloc.dart
    │   ├── validar_cuit_bloc.dart
    │   ├── motivo_rechazo_bloc.dart
    │   └── validators.dart            # Mixin con StreamTransformers
    ├── providers/                     # 6 servicios
    │   ├── muvin_provider.dart        # REST — ~25 métodos (810 líneas)
    │   ├── socket_provider.dart       # Socket.IO
    │   ├── push_notifications_provider.dart  # FCM — singleton
    │   ├── firebase_analytics_provider.dart  # Analytics
    │   ├── geolocator_provider.dart   # GPS
    │   └── connectivity_provider.dart # Verificación de red
    ├── models/
    │   ├── chofer_model.dart          # ChoferModel + Persona + Camion + Acoplado
    │   └── firebase_analytics_model.dart
    ├── share/
    │   └── preference.dart            # Singleton SharedPreferences (~355 líneas, 34 campos)
    └── utils/
        └── utils.dart                 # Utilidades generales (mostrarAlerta, etc.)

android/
├── app/
│   ├── google-services.json           # Firebase config
│   └── src/main/AndroidManifest.xml   # Permisos Android
└── ...

assets/
├── images/                            # Recursos gráficos
└── fonts/                             # Tipografías (si aplica)
```

## Credenciales hardcodeadas en `config.dart`

| Variable | Valor (fragmentado) | Tipo | Riesgo |
|----------|---------------------|------|--------|
| `apiUrl` | `https://panel.muvinapp.com/api/backend/web/` | URL producción | 🟡 Medio |
| `urlWebSocket` | `https://sockets.muvinapp.com` | URL producción | 🟡 Medio |
| `tokenMapBox` | `pk.eyJ1IjoiYWxmb25zb21jIi...` | Token público MapBox | 🟡 Medio |
| `serverToken` | `AAAAZF2L6K4:APA91bH...` | FCM Server Key privada | 🔴 **CRÍTICO** |
| `xApiKey` | `uH9hatw8G3lQls7fE3` | API Key backend | 🔴 **ALTO** |

## Dependencias completas (`pubspec.yaml`)

```yaml
dependencies:
  flutter: sdk: flutter
  flutter_localizations: sdk: flutter
  cupertino_icons: ^0.1.3
  http: ^0.12.1
  rxdart: ^0.22.6
  provider: ^4.0.4
  shared_preferences: ^0.5.6+3
  socket_io_client: ^0.9.8
  connectivity: ^3.0.3
  geolocator: ^7.0.1
  flutter_map: ^0.10.1+1
  latlong: ^0.6.1
  firebase_messaging: ^6.0.12
  firebase_analytics: ^5.0.11
  image_picker: ^0.6.6+5
  path_provider: ^1.6.7
  barcode_scan: ^1.0.0
  pdf: ^1.4.1
  intl: ^0.16.1
  validators: ^2.0.0+1
  transparent_image: ^1.0.0
  bubble: ^1.1.9+1
  barcode_flutter: ^1.1.0
  sweetalert: any
  flutter_sequence_animation: ^3.0.0
  url_launcher: ^5.4.10
  flutter_html: ^0.11.0
  mime: ^0.9.6+3
  http_parser: ^3.1.3
  android_intent: ^0.3.5+1

dev_dependencies:
  flutter_test: sdk: flutter
```

## Configuración Android

- **Package**: `com.muvinapp.muvin_app_choferes`
- **Permisos requeridos**: GPS (ACCESS_FINE_LOCATION), Internet, Camera (barcode scan), Storage
- **Firebase**: requiere `google-services.json` en `android/app/`
- **MinSDK**: compatible con Flutter 1.x (~API 16+)

---

Ver también: [[riesgos]] · [[stack-tecnologico]] · [[build-flutter]]
