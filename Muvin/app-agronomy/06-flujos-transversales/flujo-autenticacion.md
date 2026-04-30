# Flujo: Autenticación y Autorización

> **Módulos involucrados:** [[modulo-sessions]], [[modulo-shared]] (CentroAuthGuard), [[modulo-pedidos]], [[modulo-transportistas]]

## Descripción

Flujo que cubre desde el acceso inicial a la app hasta el acceso a las pantallas protegidas. Incluye la verificación de token en cada navegación a rutas privadas.

## Diagrama

```mermaid
sequenceDiagram
    actor Usuario
    participant Router as Angular Router
    participant Guard as CentroAuthGuard
    participant AuthSvc as AuthService
    participant API as API REST
    participant localStorage as localStorage

    Usuario->>Router: Navega a /pedidos
    Router->>Guard: canActivate()
    Guard->>localStorage: getItem('token')
    alt Token existe
        Guard->>localStorage: getItem('rol')
        alt Rol permitido (3, 11, 16)
            Guard-->>Router: true → carga PedidosModule
        else Rol no permitido
            Guard-->>Router: false → redirect /sessions/signin
        end
    else Token null
        Guard-->>Router: false → redirect /sessions/signin
    end

    Usuario->>Router: Navega a /sessions/signin
    Router->>AuthSvc: (componente cargado)
    Usuario->>AuthSvc: login(user, pass, captcha)
    AuthSvc->>API: POST login/login-panel
    API-->>AuthSvc: {status, data: {token, rol, ...}}
    AuthSvc->>localStorage: setItem(token, rol, currentUser, ...)
    AuthSvc-->>Router: Redirige a /pedidos
```

## Estados de sesión

```mermaid
stateDiagram-v2
    [*] --> SinSesion: App carga sin token
    SinSesion --> Autenticando: Usuario ingresa credenciales
    Autenticando --> Autenticado: Login exitoso
    Autenticando --> SinSesion: Login fallido
    Autenticado --> SinSesion: Logout / token expirado (🚧 expiración no activa)
    Autenticado --> Autenticado: Navegación entre rutas protegidas
```

## Riesgos del flujo

- 🔴 La expiración del token no se valida (guard comentado). Un token robado no expira en el frontend.
- 🔴 Bug de asignación (`=` en lugar de `===`) en el login. Ver [[security-inventory#SEC-03]].
- 🔴 Token en `localStorage`. Ver [[security-inventory#SEC-01]].
