# ConfigService — Gestión de Sesión

## Descripción

Servicio singleton que centraliza el estado de la sesión del usuario autenticado: **token JWT**, **refresh token** y **taxId** (CUIT).

## API pública

```typescript
// Estado de inicialización
available: Signal<'LOADING' | 'SUCCESS' | 'ERROR'>

// URL base del API
get url(): string

// Hidratar desde localStorage al arrancar la app
hidrate(): void

// Obtener valor
get<K extends TStorageKey>(key: K): TStorage[K]

// Actualizar valor
set<K extends TStorageKey>(key: K, value: TStorage[K]): void
```

## TStorage

```typescript
interface IConfiguration {
  readonly token: string;    // JWT access token
  readonly refresh: string;  // JWT refresh token
  readonly taxId: string;    // CUIT del usuario autenticado
}
```

## Ciclo de vida

```
1. App arranca → ConfigService.hidrate() (en app.config.ts)
2. Lee token, refresh, taxId de localStorage
3. available signal pasa de 'LOADING' → 'SUCCESS'
4. tokenInterceptorFn puede leer token con ConfigService.get('token')
```

---

# tokenInterceptorFn — Interceptor JWT

## Descripción

`HttpInterceptorFn` (función standalone) que adjunta el **Bearer token** a cada request y maneja el **refresh automático** cuando el token expira.

## Flujo

```
Request entrante
    ↓
¿Tiene token en ConfigService?
    ├── No → pasa sin headers
    └── Sí → adjunta: Authorization: Bearer {token}
              Content-Type: application/json
              Accept: application/json
                  ↓
              ¿Respuesta 401?
                  ├── No → retorna respuesta normal
                  └── Sí → ¿Ya hay refresh en curso?
                            ├── Sí → espera refreshSubject (BehaviorSubject)
                            └── No → isRefreshing = true
                                     POST /refresh con refresh token
                                     ├── Éxito → ConfigService.set('token', nuevo)
                                     │            reintenta request original
                                     └── Error → throwError (logout)
```

## Variables de estado del interceptor

```typescript
let isRefreshing: boolean = false;
const refreshSubject: BehaviorSubject<string | null>
```

## IRefresh (respuesta del endpoint de refresh)

```typescript
interface IRefresh {
  readonly success: boolean;
  readonly status: number;
  readonly data: {
    readonly access_token: string;
    readonly expires_at: number;
  };
}
```
