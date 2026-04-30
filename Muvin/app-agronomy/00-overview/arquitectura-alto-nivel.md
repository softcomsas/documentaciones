# Arquitectura de Alto Nivel — App Agronomy

> **Última revisión:** 2026-04-30

## Capas del sistema

```mermaid
graph TD
    subgraph Cliente["🌐 Cliente (Browser / PWA)"]
        AppRoot["AppComponent\n(Bootstrap)"]
        Router["Angular Router\n(app.routing.ts)"]
        AuthLayout["AuthLayoutComponent\n(rutas públicas)"]
        AdminLayout["AdminLayoutComponent\n(rutas protegidas + nav)"]
    end

    subgraph Modulos["📦 Módulos de Páginas (Lazy Loading)"]
        Sessions["SessionsModule\n/sessions/*"]
        Pedidos["PedidosModule\n/pedidos/*"]
        Transportistas["TransportistasModule\n/transportistas/*"]
    end

    subgraph SharedLayer["🧩 Capa Compartida"]
        Guards["CentroAuthGuard"]
        Interceptors["AuthInterceptor"]
        SharedServices["Servicios Globales\n(GlobalService, UserService, etc.)"]
        SharedModels["Modelos / Interfaces\n(~90 interfaces)"]
        SharedComponents["Layouts, Pipes, Directives"]
    end

    subgraph Backend["🔌 API REST (externa)"]
        API["environment.apiURL\n(Yii2 / NestJS)"]
    end

    AppRoot --> Router
    Router --> AuthLayout --> Sessions
    Router --> AdminLayout --> Guards
    Guards --> Pedidos
    Guards --> Transportistas
    Sessions --> SharedLayer
    Pedidos --> SharedLayer
    Transportistas --> SharedLayer
    SharedLayer --> API
```

## Descripción de capas

### Presentación
- **AppComponent**: raíz de la aplicación, configura tema y traducciones.
- **AuthLayoutComponent**: layout sin barra de navegación para rutas públicas (`/sessions/*`).
- **AdminLayoutComponent**: layout completo con sidebar y navbar para rutas protegidas.

### Lógica de aplicación
- Módulos lazy-loaded: `SessionsModule`, `PedidosModule`, `TransportistasModule`.
- Cada módulo encapsula sus propios componentes, servicios y modelos.
- La capa `Shared` provee servicios transversales, guards, interceptors y componentes reutilizables.

### Autenticación y seguridad
- **CentroAuthGuard**: valida token en `localStorage` y filtra por rol (`3`, `11`, `16`). Si no hay token, redirige a `/sessions/signin`.
- **AuthInterceptor**: ⚠️ Pendiente de verificar si existe un interceptor HTTP que inyecte el token en los headers.
- Token almacenado en `localStorage` (clave `token`). Sin expiración activa validada en el guard actualmente. 🔴

### Datos
- La app es **stateless en el frontend**: no usa NGXS, Redux ni ningún store reactivo.
- El estado se gestiona con servicios + `BehaviorSubject` / `EventEmitter` puntualmente.
- Toda persistencia está en la **API REST** externa.

### Integración
- `environment.apiURL` define el host del backend (configurado por ambiente: dev/prod).
- Google Maps API integrada via `@angular/google-maps`.
- Service Worker para capacidades PWA offline.
