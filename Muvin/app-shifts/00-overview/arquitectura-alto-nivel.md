# Arquitectura de alto nivel — app-shifts

## Diagrama general

```
┌──────────────────────────────────────────────────────────┐
│                      app-shifts (Flutter)                │
│                                                          │
│  ┌──────────┐   ┌─────────────┐   ┌──────────────────┐  │
│  │  Pages   │──►│    BLoCs    │──►│   Providers      │  │
│  │ (26 UI)  │   │ (13 bloques)│   │ (6 servicios)    │  │
│  └──────────┘   └─────────────┘   └──────────────────┘  │
│        │               │                   │             │
│  ┌─────▼───────────────▼───────────────────▼──────────┐  │
│  │         InheritedWidget (Provider)                  │  │
│  │   Singleton — instancia única en árbol de widgets   │  │
│  └────────────────────────────────────────────────────┘  │
│                          │                               │
│  ┌───────────────────────▼──────────────────────────┐    │
│  │              SharedPreferences                   │    │
│  │     (persistencia local — Preferences class)     │    │
│  └──────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────┘
         │                     │                  │
         ▼                     ▼                  ▼
  REST API Backend       Socket.IO          Firebase FCM
  panel.muvinapp.com   sockets.muvinapp.com  + Analytics
```

## Capa de páginas (UI)

- **26 páginas** en `lib/src/pages/`
- Navegación con rutas nombradas: `Routes.dart` + `FadePageRoute` (transición personalizada)
- Cada página consume uno o más BLoCs vía `Provider.of(context)`

## Capa BLoC

- **13 BLoCs** basados en streams RxDart (`BehaviorSubject`)
- No se usa `flutter_bloc` — es implementación manual con `rxdart`
- Patrón: `Stream` de salida + `Function(T)` como sink de entrada + `BehaviorSubject.value` para último valor
- Validaciones compartidas via `mixin Validators` (`validators.dart`)
- Todos los BLoCs expuestos globalmente desde `blocs/provider.dart` (InheritedWidget)

## Capa de providers (servicios)

| Provider | Responsabilidad |
|----------|----------------|
| `MuvinProvider` | Todas las llamadas REST al backend |
| `SocketProvider` | Conexión Socket.IO — tiempo real |
| `PushNotificationsProvider` | Firebase Messaging (FCM) — singleton |
| `FirebaseAnalyticsProvider` | Tracking de eventos con Firebase Analytics |
| `GeolocatorProvider` | Geolocalización GPS del chofer |
| `ConnectivityProvider` | Verificación de conectividad antes de cada request |

## Persistencia local

La clase `Preferences` (singleton) encapsula **`SharedPreferences`** con ~30 campos tipados:

- Credenciales: `accessToken`, `tokenNotificacion`
- Perfil: `nombre`, `apellidos`, `email`, `telefono`, `cuitCuil`, `dni`, `domicilio`
- Equipo: `patenteCamion`, `patenteAcoplado`, `idTipoCamion`, `idMarcaCamion`, etc.
- Viaje activo: `idViaje`, `idEstado`, `idDestino`, `cupo`, `producto`, `turneada`
- Geo: `latitude`, `longitude`
- Configuración: `ultimaPagina`, `disponible`, `pais`, `provincia`, `localidad`

## Flujo de datos típico

```
Usuario interactúa con Page
  → Page llama BLoC.changeFoo(valor)
    → BLoC valida via StreamTransformer
      → Page escucha StreamBuilder y habilita Submit
        → Page llama Provider.metodo(bloc)
          → Provider verifica ConnectivityProvider
            → Provider hace HTTP request con Bearer token
              → Resultado: {ok: true/false, data/errores}
                → Page actualiza UI o muestra error
```

## Manejo de errores HTTP

`MuvinProvider` centraliza el manejo de errores:
- **200-299**: `{ok: true, data: ...}`
- **401**: tratado individualmente por método (re-login o datos nulos)
- **422**: `{ok: false, errores: [{field, message}]}` — errores de validación
- **Otros**: mensaje genérico
- **Sin conexión**: mensaje específico via `ConnectivityProvider`
- **Excepción Dart**: `retornarErrorDesconocido()`

---

Ver también: [[stack-tecnologico]] · [[vision-general]] · [[inventario-general]]
