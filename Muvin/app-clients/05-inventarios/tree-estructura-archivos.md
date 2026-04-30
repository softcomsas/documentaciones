# Árbol de Estructura de Archivos — app-clients

> [[README]]

## Estructura completa del proyecto

```
app-clients/
├── pubspec.yaml                    # Dependencias y metadatos Flutter
├── pubspec.lock
├── lib/
│   ├── main.dart                   # Entry point; init Firebase, Socket, orientación
│   └── src/
│       ├── blocs/                  # Capa BLoC (lógica de negocio)
│       │   ├── provider.dart       # BlocProvider (InheritedWidget genérico)
│       │   ├── validators.dart     # Transformadores RxDart (email, required)
│       │   ├── login_bloc.dart     # Validación + llamada login
│       │   ├── pedido_bloc.dart    # Estado pedidos/cargas
│       │   ├── busqueda_bloc.dart  # Búsqueda de cupos por demandante
│       │   ├── solicitud_cupo_bloc.dart    # CRUD solicitudes de cupo
│       │   ├── navegacion_cupos_bloc.dart  # Navegación/paginación cupos
│       │   ├── cabecera_bloc.dart          # Selector de cabeceras
│       │   └── modificar_cant_cupo_bloc.dart  # Variar cantidad demanda
│       ├── config/
│       │   ├── config.dart         # 🔴 URL, X-Api-Key, serverToken, wsUrl hardcodeados
│       │   └── preference.dart     # Acceso a SharedPreferences
│       ├── menu/
│       │   └── menu.dart           # Drawer con ítems condicionales por perfil
│       ├── models/
│       │   ├── solicitud_model.dart
│       │   ├── solicitud_card_model.dart
│       │   ├── alfanumerico_model.dart
│       │   └── firebase_analytics_model.dart
│       ├── pages/
│       │   ├── splash_page.dart            # Auto-login check
│       │   ├── login_page.dart             # Formulario login
│       │   ├── terminos_page.dart          # T&C
│       │   ├── home_page.dart              # Hub principal + Socket + FCM
│       │   ├── cupos_page.dart             # Entrada módulo Cupos (Admin)
│       │   ├── cuposcf_page.dart           # Entrada módulo Cupos (CF)
│       │   ├── gestion_carga_page.dart     # Entrada módulo Cargas
│       │   ├── carga_cupos_disponibles_page.dart
│       │   ├── carga_nuevo_pedido_page.dart
│       │   ├── cupos/
│       │   │   ├── gestion_page.dart
│       │   │   ├── solicitudes_page.dart
│       │   │   ├── solicitud_page.dart
│       │   │   ├── solicitud_detail_page.dart
│       │   │   └── busqueda_page.dart
│       │   ├── cargas/                     # Sub-páginas de cargas
│       │   ├── cupos_cfinal/               # Sub-páginas cliente final
│       │   └── widgets/                    # Widgets compartidos entre páginas
│       ├── providers/
│       │   └── muvin_provider.dart         # Cliente HTTP; todos los endpoints
│       ├── routes/
│       │   └── routes.dart                 # Mapa de rutas nombradas
│       └── share/                          # Helpers, formateadores, utils
├── android/
├── ios/
└── test/
```

## Estadísticas del proyecto

| Métrica | Valor |
|---------|-------|
| Archivos Dart | ~35 |
| BLoCs | 9 |
| Páginas/Screens | ~15 |
| Modelos | 4 |
| Endpoints HTTP | ~20 |
| Dependencias directas | 28 |
| Dependencias deprecadas | 6 🔴 |
