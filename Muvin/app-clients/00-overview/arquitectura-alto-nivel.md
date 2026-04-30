# Arquitectura de Alto Nivel — App Clients

> **Última revisión:** 2026-04-30

## Patrón arquitectónico

La app sigue el patrón **BLoC (Business Logic Component)** con **RxDart** para streams reactivos. La capa de UI consume BLoCs inyectados vía `Provider` (propio, no el paquete). Los BLoCs delegan las llamadas HTTP a `MuvinProvider`.

```mermaid
graph TD
    subgraph UI["📱 Capa de Presentación (Widgets)"]
        Pages["Pages\n(Stateful/Stateless Widgets)"]
        Menu["Menu lateral\n(Drawer)"]
    end

    subgraph BLoC["🔄 Capa BLoC (Lógica de negocio)"]
        LoginBloc["LoginBloc"]
        CupoBloc["SolicitudCupoBloc"]
        PedidoBloc["PedidoBloc"]
        BusquedaBloc["BusquedaBloc"]
        NavBloc["NavegacionCuposBloc"]
        CabeceraBloc["CabeceraBloc"]
        ModifBloc["ModificarCantCupoBloc"]
    end

    subgraph Data["💾 Capa de Datos"]
        MuvinProv["MuvinProvider\n(HTTP client)"]
        Prefs["Preferences\n(SharedPreferences)"]
        Config["Config\n(constantes: apiUrl, keys)"]
    end

    subgraph External["🔌 Servicios Externos"]
        API["API REST\npanel.muvinapp.com"]
        Firebase["Firebase\nAnalytics + FCM"]
        Socket["Socket.IO\nsocket.muvinapp.com"]
        Geo["Geolocator"]
    end

    Pages --> BLoC
    Menu --> Prefs
    BLoC --> MuvinProv
    BLoC --> Prefs
    MuvinProv --> API
    MuvinProv --> Config
    MuvinProv --> Prefs
    Pages --> Firebase
    Pages --> Socket
    Pages --> Geo
```

## Ciclo de vida de autenticación

```mermaid
sequenceDiagram
    actor Usuario
    participant App as main.dart
    participant Splash as SplashScreen
    participant Prefs as Preferences
    participant Login as LoginPage
    participant Bloc as LoginBloc
    participant API as API REST

    App->>Prefs: initPrefs()
    App->>Splash: show()
    Splash->>Prefs: get ultimaPagina
    alt Token válido
        Splash-->>App: navigate /home
    else Sin token
        Splash-->>App: navigate /login
    end
    Usuario->>Login: username + password
    Login->>Bloc: login(user, pass)
    Bloc->>API: POST login/login-panel
    API-->>Bloc: {authorization_code, id_rol}
    Bloc->>API: POST login/access-token
    API-->>Bloc: {access_token, refresh_token, persona, ...}
    Bloc->>Prefs: guarda tokens + perfil
    Bloc-->>Login: navigate /home
```

## Gestión de tokens

- **Access Token:** JWT guardado en `SharedPreferences` (clave `access_token`). Se envía en header `Authorization: Bearer <token>`.
- **Refresh Token:** guardado en `SharedPreferences` (clave `refresh_token`). Se usa automáticamente cuando el backend devuelve `401`.
- **X-Api-Key:** hardcodeada en `Config.xApiKey`. Se envía en todas las peticiones autenticadas.

> [!danger] Riesgo de seguridad
> `Config.dart` contiene la API key, server token de FCM y URL de WebSocket **hardcodeados en el código fuente**. Ver [[security-inventory]].
