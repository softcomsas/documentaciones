# Flujo: Autenticación y Acceso a MFE

> **Módulos involucrados:** [[modulo-auth]], [[modulo-main-shell]], [[modulo-shared]]
> **Tipo:** Flujo de seguridad / acceso

## Diagrama de Secuencia

```mermaid
sequenceDiagram
    actor Usuario
    participant Browser
    participant Shell as main (Shell Angular)
    participant AuthMFE as auth-app (MFE Login)
    participant AuthBE as Backend Autenticación
    participant NGXS as NGXS Store (shared)
    participant MFE as MFE destino (ej: descargas-app)
    participant MFE_BE as Backend del MFE

    Usuario->>Browser: Accede a la plataforma
    Browser->>Shell: Carga main bundle
    Shell->>Shell: Guard evalúa sesión activa
    alt Sin sesión activa
        Shell->>AuthMFE: loadRemoteModule(auth-app)
        AuthMFE->>Usuario: Muestra pantalla de login
        Usuario->>AuthMFE: Ingresa credenciales
        AuthMFE->>AuthBE: POST /login (credenciales)
        AuthBE-->>AuthMFE: JWT token + datos de usuario
        AuthMFE->>NGXS: Dispatch SetUser(token, userData)
        NGXS->>Browser: Persiste token (localStorage via @ngxs/storage-plugin)
        AuthMFE->>Shell: Navega a ruta destino
    else Sesión activa (token en storage)
        Shell->>NGXS: Rehydrate state del storage
    end
    Shell->>MFE: loadRemoteModule(mfe-destino) via lazy route
    MFE->>MFE: Guard verifica token en NGXS Store
    MFE->>MFE_BE: HTTP request con Bearer token
    MFE_BE->>MFE_BE: TokenMiddleware valida JWT
    MFE_BE-->>MFE: Respuesta con datos
    MFE-->>Usuario: Muestra la pantalla
```

## Notas

- ⚠️ El backend de `auth-app` es un stub. La autenticación real puede estar en el backend Yii2 de alguno de los módulos legacy o en un servicio externo. **Requiere verificación urgente.**
- El token JWT se persiste en `localStorage` via `@ngxs/storage-plugin`.
- Cada backend NestJS valida el JWT en `TokenMiddleware` (`forRoutes('*')`).
- Los backends Yii2 tienen su propio mecanismo de autenticación (`SiteController`) que puede no estar integrado con el mismo JWT.
