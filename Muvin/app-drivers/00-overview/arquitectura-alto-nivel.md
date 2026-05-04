# Arquitectura de Alto Nivel — app-drivers

> [[vision-general]] | [[stack-tecnologico]]

## Diagrama de capas

```mermaid
graph TD
    subgraph "Capa de presentación (Pages)"
        SPLASH[SplashScreen]
        LOGIN[LoginPage]
        PHONE[PhoneVerificationPage]
        INTRO[IntroPage]
        HOME[HomePage]
        CARGAS[CargasPage]
        CDETAIL[CargasDetallePage]
        POST[PostulacionesPage]
        VIAJE[ViajePage]
        CARTA[CartaPortePage 💀]
        SIGNUP[SignupPage 💀]
    end

    subgraph "Capa de estado (BLoC)"
        PROV[Provider InheritedWidget]
        REGBLOC[RegisterBloc\ncountry + phone streams]
        PHONEBLOC[PhoneCodeBloc\ncode stream]
    end

    subgraph "Capa de datos (Providers)"
        MUVIN[MuvinProvider\nHTTP REST]
        GEO[GeolocatorProvider\nGPS nativo]
        PREFS[Preferences\nSharedPreferences Singleton]
    end

    subgraph "Config"
        CFG[Config\napiUrl, tokenMapBox]
    end

    subgraph "Backend externo"
        API[api-panel Yii2\npruebas.muvinapp.com]
    end

    HOME -->|Timer 10s| GEO
    HOME --> MUVIN
    CARGAS --> MUVIN
    POST --> MUVIN
    VIAJE --> MUVIN
    CARTA --> MUVIN
    LOGIN --> PROV
    PROV --> REGBLOC
    PROV --> PHONEBLOC
    MUVIN --> CFG
    MUVIN --> PREFS
    MUVIN --> API
    SPLASH --> PREFS
```

## Patrón arquitectónico

La app usa un **BLoC manual con InheritedWidget** (sin `flutter_bloc` ni `provider` package). Solo el flujo de login tiene BLoC; el resto de pantallas acceden directamente a `MuvinProvider` sin gestión de estado reactiva.

## Routing

Routing **nombrado** gestionado por `routes.dart` con transición `FadeTransition`. Las rutas disponibles son:

| Ruta | Page |
|------|------|
| `/` (default) | `HomePage` |
| `/login` o `/logout` | `LoginPage` |
| `/phoneverification` | `PhoneVerificationPage` |
| `/signup` | `SignupPage` (vacía) |
| `/intro` | `IntroPage` |
| `/home` | `HomePage` |
| `/cargas` | `CargasPage` |
| `/cargasdetalle` | `CargasDetallePage` |
| `/postulaciones` | `PostulacionesPage` |
| `/viaje` | `ViajePage` |
| `/cartaporte` | `CartaPortePage` (rota) |
