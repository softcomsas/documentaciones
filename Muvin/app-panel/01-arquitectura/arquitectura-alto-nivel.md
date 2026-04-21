# Arquitectura de Alto Nivel

> **Proyecto:** Muvinapp (app-panel)
> **Última revisión:** 2026-04-16
> **Framework:** Angular 6.0.1 · Egret v6.0.1 · TypeScript 2.7.2

---

## Diagrama de capas

```mermaid
graph TB
    subgraph BROWSER["🌐 Navegador"]
        direction TB
        UI["UI Layer<br/>Angular Material + Kendo Grid<br/>Egret Layout System"]
        STATE["State Layer<br/>localStorage (40+ keys)<br/>BehaviorSubject streams"]
        SERVICES["Service Layer<br/>~70+ servicios shared<br/>GlobalService hub"]
        INTERCEPTORS["HTTP Interceptor Chain<br/>JwtInterceptor → ErrorInterceptor"]
    end

    subgraph BACKENDS["☁️ Backends"]
        API_MAIN["API Principal<br/>apiURL → /api/backend/web/"]
        API_NEW["API Nueva<br/>URL_SERVICIOS → /muvinapp-new-api/"]
        WS["WebSocket Server<br/>wsUrl → /socket/"]
        FIREBASE["Firebase Firestore<br/>colección: configuracion-panel"]
    end

    UI --> STATE
    UI --> SERVICES
    SERVICES --> INTERCEPTORS
    INTERCEPTORS --> API_MAIN
    SERVICES -.->|directo| API_NEW
    UI --> |Socket.IO| WS
    SERVICES --> |AngularFirestore| FIREBASE
```

---

## 1. Bootstrap y carga inicial

```mermaid
sequenceDiagram
    participant B as Browser
    participant A as AppModule
    participant IM as InMemoryWebApiModule
    participant JWT as JwtInterceptor
    participant ERR as ErrorInterceptor

    B->>A: Carga index.html + main.ts
    A->>A: platformBrowserDynamic().bootstrapModule(AppModule)
    A->>IM: Registra mock endpoints (passThruUnknownUrl: true)
    A->>JWT: Registra HTTP_INTERCEPTORS[0]
    A->>ERR: Registra HTTP_INTERCEPTORS[1]
    A->>B: Renderiza AppComponent → Router
```

**`AppModule` providers raíz:**
| Provider | Tipo | Rol |
|---|---|---|
| `AuthService` | Singleton | Autenticación, BehaviorSubject de menú y usuarios |
| `UserService` | Singleton | Datos de usuario |
| `GlobalService` | Singleton | Wrapper de `environment.apiURL` → `apiHost` |
| `MatbatService` | Singleton | BehaviorSubject + localStorage para parámetro MATBA |
| `JwtInterceptorService` | HTTP_INTERCEPTORS | Adjunta `Bearer` token a cada request |
| `ErrorInterceptor` | HTTP_INTERCEPTORS | Maneja 401/403/400/404/0 |

> [!warning] InMemoryWebApiModule en producción
> `InMemoryWebApiModule.forRoot(InMemoryDataService, { passThruUnknownUrl: true })` corre en todos los ambientes. Los mock endpoints del chat (`contacts`, `chat-collections`, `chat-user`) se sirven localmente; las URLs no reconocidas pasan al backend real. Riesgo: una URL de mock podría shadear un endpoint real si los nombres colisionan.

---

## 2. Sistema de layout

```mermaid
graph LR
    subgraph Router
        R[app.routing.ts]
    end

    subgraph Layouts
        AUTH[AuthLayoutComponent<br/>Shell vacío]
        ADMIN[AdminLayoutComponent<br/>Sidebar + Header + Breadcrumb]
    end

    subgraph Modes["Modos de navegación"]
        SIDE["side → SidebarSide + HeaderSide"]
        TOP["top → SidebarTop + HeaderTop"]
    end

    R -->|"/sessions/*"| AUTH
    R -->|"/* (autenticado)"| ADMIN
    ADMIN --> SIDE
    ADMIN --> TOP
```

**`AdminLayoutComponent`** controla dos modos de navegación vía `LayoutService.layoutConf.navigationPos`:

| Propiedad | Default | Opciones |
|---|---|---|
| `navigationPos` | `"side"` | `"side"` · `"top"` |
| `sidebarStyle` | `"compact"` | `"full"` · `"compact"` · `"closed"` |
| `dir` | `"ltr"` | `"ltr"` · `"rtl"` |
| `topbarFixed` | `true` | boolean |
| `useBreadcrumb` | `false` | boolean |

---

## 3. Flujo de autenticación

```mermaid
sequenceDiagram
    participant U as Usuario
    participant S as SigninComponent
    participant AS as AuthService
    participant API as Backend API
    participant LS as localStorage

    U->>S: Ingresa usuario + contraseña + captcha
    S->>AS: login(username, password, captcha)
    AS->>API: POST /login/login-panel
    API-->>AS: Pre-session data
    AS->>API: POST /login/access-token
    API-->>AS: { access_token, refresh_token, menu, persona, ... }
    AS->>LS: SetLocalStorage() → 40+ keys
    Note over LS: token, refresh_token, rol,<br/>currentUser, accept_tyc,<br/>cuit_cuil, nameUser, id_centro,<br/>flags de módulos, etc.
    AS->>U: Router.navigate → /home o ruta original
```

**Autenticación 100% JWT custom** — Firebase NO se usa para auth.

### Cadena de interceptores HTTP

```mermaid
graph LR
    REQ[HttpRequest] --> JWT["JwtInterceptor<br/>+ Bearer token"]
    JWT --> ERR["ErrorInterceptor<br/>catch 401/403/400/0"]
    ERR --> API["Backend"]
    API -->|200| RES[HttpResponse]
    API -->|401| ERR
    ERR -->|401| LOGOUT["authService.logout()<br/>+ location.reload()"]
    API -->|403| ERR
    ERR -->|403| DIALOG["AppAtencionService<br/>modal"]
    API -->|0| ERR
    ERR -->|0| MSG["'No hay conexión<br/>con el servidor'"]
```

> [!info] Doble manejo de 401
> Ambos interceptores manejan el 401: `JwtInterceptor` navega a `/sessions/signin`; `ErrorInterceptor` hace `authService.logout()` + `location.reload()`. En la práctica, `ErrorInterceptor` gana porque su `catchError` cortocircuita el `tap` del JWT interceptor.

### Guards de rol

| Guard | Roles (`localStorage.rol`) | Módulos protegidos |
|---|---|---|
| `AuthGuard` | Cualquier token válido | Cupo, Cupera, Turneada, Combustible, Documentos, Destinatario |
| `TermAuthGuard` | Token + `accept_tyc === '1'` | Todos los autenticados |
| `AdminAuthGuard` | `1`, `12` | Admin |
| `CentroAuthGuard` | `3`, `11`, `16` | Centro, CCPP |
| `TranspAuthGuard` | `4` | Transportista |
| `DadorAuthGuard` | (roles de dador) | Dador |
| `DestinoAuthGuard` | (roles de destino) | Destino |
| `MagypAuthGuard` | (roles de MAGyP) | MAGyP |
| `MtrAuthGuard` | (roles de MTR) | MTR |
| `FertilizantesAuthGuard` | `15` | Fertilizante |

---

## 4. Capa de estado

```mermaid
graph TB
    subgraph "Estado del sistema"
        LS["localStorage<br/>40+ keys<br/>(token, rol, flags...)"]
        SS["sessionStorage<br/>frontend-setting"]
        BS["BehaviorSubject streams<br/>AuthService._menuSubject$<br/>LayoutService.layoutConfSubject<br/>MatbatService"]
        FB["Firebase Firestore<br/>configuracion-panel/*"]
    end

    subgraph "NO utilizado"
        NGRX["@ngrx/store ^8.4.0<br/>⚠️ Instalado pero sin<br/>reducers, actions ni effects"]
    end

    LS --> |Guards leen rol| GUARDS[Auth Guards]
    LS --> |Servicios leen token| INTERCEPTORS2[JwtInterceptor]
    BS --> |UI reacciona| COMPONENTS[Componentes]
    SS --> |Config global| GS[GlobalService.setting]
    FB --> |Config centro| FIRE[FireserviService]
```

> [!warning] NgRx es dead code
> `@ngrx/store ^8.4.0` está instalado pero **nunca configurado**. No hay `StoreModule.forRoot()`, ni reducers, ni actions, ni effects. Además la versión 8 es incompatible con Angular 6 (requiere Ng8+). Todo el estado vive en `localStorage` + `BehaviorSubject`.

---

## 5. Canales de comunicación con backends

```mermaid
graph LR
    subgraph APP["Angular App"]
        HC[HttpClient + Interceptors]
        WS[WebsocketService<br/>Socket.IO]
        FS[FireserviService<br/>AngularFirestore]
    end

    subgraph BACKENDS
        B1["API Principal<br/>apiURL"]
        B2["API Nueva<br/>URL_SERVICIOS"]
        B3["WebSocket Server<br/>wsUrl + /socket/socket.io/"]
        B4["Firebase Firestore<br/>muvinapp-89561"]
    end

    HC -->|"REST (JWT Bearer)"| B1
    HC -->|"REST (directo)"| B2
    WS -->|"Socket.IO events"| B3
    FS -->|"Firestore SDK"| B4
```

| Canal | URL (dev) | Autenticación | Uso |
|---|---|---|---|
| **API Principal** | `https://dev.muvinapp.com/api/backend/web/` | JWT Bearer via interceptor | CRUD de todas las entidades (~60 servicios) |
| **API Nueva** | `https://dev.muvinapp.com/muvinapp-new-api/` | Directo (sin interceptor en algunos casos) | Endpoints v2/v3 (CuperaService) |
| **WebSocket** | `https://dev.muvinapp.com/socket/` | `configurar-usuario` al conectar | Eventos en tiempo real (notificaciones, estado) |
| **Firebase** | `muvinapp-89561.firebaseapp.com` | Firebase SDK key | Solo `configuracion-panel` (config por centro) |

### WebSocket — eventos conocidos

```typescript
// Al conectar, el servicio se auto-registra:
socket.emit('configurar-usuario', {
  id_persona: response.data.id_persona,
  nombre: localStorage.getItem('nameUser'),
  consultas: true
});

// Consumidores escuchan eventos vía:
websocketService.listen('nombre-evento')  // → Observable<any>
```

Componentes activos que usan WebSocket:
- `FooterSocketComponent` — indicador de conexión
- `SidebarSideComponent` — notificaciones
- `SidenavComponent` — eventos de sidebar
- `SidebarTopComponent` — notificaciones

---

## 6. SharedModule — el mega-módulo

```mermaid
graph TB
    SM["SharedModule<br/>~60 declarations<br/>~35 entryComponents<br/>~10 providers"]

    SM --> |declara| LAYOUT["Layout chrome<br/>HeaderSide, SidebarSide,<br/>AdminLayout, AuthLayout"]
    SM --> |declara| DIALOGS["Dialogs<br/>AppConfirm, AppAlert,<br/>AppError, AppAtencion,<br/>AppLoader"]
    SM --> |declara| PIPES["Pipes & Directives<br/>CuitValidator, FontSize,<br/>ScrollTo, etc."]
    SM --> |declara| BIZ["Business components<br/>⚠️ de admin, fertilizante,<br/>ccpp, cupera"]
    SM --> |provee| GUARDS2["Auth Guards (10)"]
    SM --> |provee| DIALOG_SVC["Dialog Services (5)"]
    SM --> |provee| UI_SVC["UI Services<br/>Theme, Layout, Nav, Route"]
    SM --> |importa| MAT["30+ Mat*Modules"]
    SM --> |importa| FLEX["FlexLayoutModule"]
    SM --> |importa| AGM["AgmCoreModule"]
```

> [!danger] SharedModule declara componentes de view modules
> `SharedModule` importa y declara componentes de `views/admin`, `views/fertilizante`, `views/ccpp` y `views/cupera`. Esto crea **acoplamiento bidireccional** y anula los beneficios del lazy loading. Ver [[cross-module-dependencies]] para el detalle.

---

## 7. Lazy loading — módulos feature

```mermaid
graph TB
    APP[AppModule] --> |loadChildren| ADMIN[AdminModule<br/>95 rutas]
    APP --> |loadChildren| HOME[HomeModule<br/>19 rutas]
    APP --> |loadChildren| CUPO[CupoModule<br/>7 rutas]
    APP --> |loadChildren| SESSIONS[SessionsModule<br/>6 rutas]
    APP --> |loadChildren| DESTINO[DestinoModule<br/>4 rutas]
    APP --> |loadChildren| FERT[FertilizanteModule<br/>4 rutas]
    APP --> |loadChildren| MAGYP[MagypModule<br/>4 rutas]
    APP --> |loadChildren| TURNEADA[TurneadaModule<br/>4 rutas]
    APP --> |loadChildren| MTR[MtrModule<br/>3 rutas]
    APP --> |loadChildren| CUPERA[CuperaModule<br/>1 ruta]
    APP --> |loadChildren| CCPP[CcppModule<br/>1 ruta]
    APP --> |loadChildren| OTROS["+ 10 módulos más"]
```

Todos los feature modules usan lazy loading con sintaxis de string (Angular 6):
```typescript
loadChildren: './views/admin/admin.module#AdminModule'
```

---

## 8. Resumen de decisiones arquitectónicas

| Decisión | Implementación | Estado |
|---|---|---|
| Autenticación | JWT custom con localStorage | ✅ Funcional |
| Autorización | Guards de rol numérico | ✅ Funcional |
| Estado global | localStorage (40+ keys) + BehaviorSubject | ⚠️ Frágil — sin tipado ni validación |
| Comunicación REST | HttpClient + 2 interceptores | ✅ Funcional |
| Comunicación real-time | Socket.IO vía ngx-socket-io | ✅ Funcional (uso limitado) |
| Firebase | Solo Firestore para config-panel | ✅ Funcional (alcance mínimo) |
| State management | NgRx instalado pero no usado | 🔴 Dead dependency |
| Lazy loading | Todos los feature modules | ⚠️ Parcialmente anulado por SharedModule |
| Mock API | InMemoryWebApiModule en producción | ⚠️ Riesgo de shadowing |
| UI framework | Angular Material + Kendo Grid | ✅ Funcional |
| Layout | Egret template con 2 modos (side/top) | ✅ Funcional |

---

## Referencias

- [[stack-tecnologico]] — Dependencias y versiones
- [[functional-classification]] — Clasificación funcional de módulos
- [[cross-module-dependencies]] — Dependencias cruzadas entre módulos
- [[depends-matrix]] — Matriz de dependencias
- [[data-files-index]] — Índice de modelos y servicios
