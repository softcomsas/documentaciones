# Flujo de arranque — app-shifts

## Secuencia de inicialización

```
main() async
  ├── WidgetsFlutterBinding.ensureInitialized()
  ├── await Preferences().initPrefs()          // carga SharedPreferences
  └── runApp(MyApp())

MyApp
  └── Provider (InheritedWidget — singleton)
        └── MaterialApp
              ├── navigatorObservers: [FirebaseAnalyticsProvider.getAnalyticsObserver()]
              ├── localizationsDelegates: [ES, EN]
              ├── home: SplashScreen()
              └── routes: Routes.getRoutes()    // 28 rutas nombradas

SplashScreen.initState()
  ├── PushNotificationsProvider().initNotifications()
  │     ├── FCM.requestPermissions()
  │     ├── FCM.getToken() → Preferences.tokenNotificacion = token
  │     └── FCM.configure(onMessage, onLaunch, onResume)
  │
  └── SocketProvider().init()                  // (si se inicializa en splash)
        ├── io(Config.urlWebSocket, {transports: ['websocket']})
        └── socket.on('respuesta-consulta', handler)

  → Evalúa Preferences.accessToken
      ├── tiene token → Navigator.pushNamed('/home')
      └── sin token   → Navigator.pushNamed('/login')
```

## Árbol de widgets en runtime

```
Provider (InheritedWidget)
└── MaterialApp
    └── Navigator
        └── Página activa
            ├── accede a BLoCs via Provider.xxxBloc(context)
            ├── llama a MuvinProvider.metodo()
            └── escucha PushNotificationsProvider.mensajes (Stream)
```

## Inicialización de Provider (InheritedWidget)

El `Provider` es un **singleton** — solo se crea una instancia en toda la vida de la app. Expone 12 BLoCs:

```dart
Provider._internal() crea instancias de:
  RegisterBloc, PhoneCodeBloc, CartaPorteBloc, UsuarioBloc,
  CompartirBloc, CaladaBloc, ConsultaBloc, TurnoBloc,
  DenunciaSiniestroBloc, FiltroBloc, ValidarCuitBloc, MotivoRechazoBloc
```

## Flujo de navegación post-login

```
HomePage
  ├── Escucha PushNotificationsProvider.mensajes (StreamBuilder)
  │     → nueva notificación → actualiza badge o navega
  │
  ├── MuvinProvider.obtenerMiViaje() → si tiene viaje activo → muestra estado
  ├── MuvinProvider.obtenerPropuestaViaje() → si hay propuesta → muestra modal
  ├── MuvinProvider.actualizarPosicion({lat, lng}) → envía GPS periódicamente
  │
  └── Menú lateral / Bottom nav:
        Cargas → /cargas
        Viaje → /viaje
        Turno → /turno
        Consultas → /consultas
        Novedades → /novedades
        Perfil → /usuario
        Seguros → /seguros
        Beneficios → /beneficios
        Compartir → /compartir
        Logout → /logout → limpia Preferences → /login
```

---

Ver también: [[autenticacion-sms]] · [[push-notifications]] · [[socket]] · [[gps-disponibilidad]]
