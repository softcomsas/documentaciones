# Árbol de Estructura de Archivos

> **Sección:** 05-inventarios
> **Proyecto:** app-drivers

```
app-drivers/
├── pubspec.yaml                        # Dependencias Flutter 1.x
├── lib/
│   ├── main.dart                       # Entry point, Provider root
│   ├── theme/
│   │   └── style.dart                  # TextStyle constants
│   └── src/
│       ├── config/
│       │   └── config.dart             # 🔴 apiUrl + tokenMapBox hardcodeados
│       ├── routes/
│       │   └── routes.dart             # 11 rutas nombradas + FadeTransition
│       ├── share/
│       │   └── preference.dart         # Singleton SharedPreferences
│       ├── utils/
│       │   └── utils.dart              # isNumeric, formatFecha, mostrarAlerta
│       ├── providers/
│       │   ├── muvin_provider.dart     # Todos los endpoints REST (275 líneas)
│       │   └── geolocator_provider.dart # Wrapper geolocator
│       ├── blocs/
│       │   ├── provider.dart           # InheritedWidget singleton
│       │   ├── register_bloc.dart      # BLoC login (país + teléfono)
│       │   └── phone_code_bloc.dart    # BLoC código SMS
│       └── pages/
│           ├── splashscreen_page.dart  # Splash 3s, permisos, decisión de ruta
│           ├── intro_page.dart         # Intro/onboarding
│           ├── login_page.dart         # Formulario login (296 líneas)
│           ├── phone_verification_page.dart  # Validación código SMS
│           ├── signup_page.dart        # 💀 DEAD CODE — solo Container()
│           ├── home_page.dart          # Home + GPS Timer (279 líneas)
│           ├── cargas_page.dart        # Lista cargas swiper (423 líneas)
│           ├── cargas_detalle_page.dart # Detalle + postularse
│           ├── postulaciones_page.dart # Mis postulaciones (346 líneas)
│           ├── viaje_page.dart         # Detalle viaje asignado (270 líneas)
│           └── carta_porte_page.dart   # 🔴 CRASH — File _image sin init
├── android/                            # Configuración Android nativa
├── ios/                                # Configuración iOS nativa
└── assets/                             # Imágenes y fuentes
```

## Estadísticas

| Categoría | Cantidad |
|-----------|----------|
| Páginas | 11 |
| Providers | 2 |
| BLoCs | 2 (+1 InheritedWidget) |
| Endpoints REST | 11 |
| Páginas rotas/dead code | 2 |
| Líneas de código (aprox) | ~2500 |
