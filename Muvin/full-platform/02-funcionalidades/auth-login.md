# F-13 — Autenticación y Login

## Descripción

Módulo de autenticación de `auth-app` que implementa el flujo de **login con usuario/contraseña + reCAPTCHA**, activación de rol y emisión de JWT para el acceso a las apps del monorepo.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend escritorio | `auth-app/frontend/src/app/auth/login/` |
| Frontend mobile | `auth-app/frontend/src/app/auth/login-mobile/` |
| Servicios auth | `auth-app/frontend/src/app/auth/services/` |
| Backend auth | `ms-auth/` (microservicio dedicado) |

## Flujo de autenticación

```
1. Usuario ingresa username + password + captcha
2. authenticationService.login(username, password, captcha)
   └─ POST → ms-auth → valida credenciales → retorna { authorization_code, roles[] }
3. storageService.saveUserLoginPanel(userLogin)
4. Filtra roles (excluye rol id=10)
5. Si tiene al menos 1 rol válido:
   └─ authenticationService.activate({ authorization_code, rol_id: roles[0].id })
       └─ POST → ms-auth → retorna JWT de sesión
6. storageService.saveUser(user) → navigate('/')
7. Si no tiene roles válidos → error "Usuario no válido"
```

## Formulario

```typescript
form = FormGroup({
  username: ['', Validators.required],
  password: ['', Validators.required],
  captcha: ['', Validators.required],   // reCAPTCHA v3 (deshabilitado temporalmente)
})
```

## Internacionalización

- Detecta `window.navigator.language`
- Soporta: `es-ES`, `es`, `en`
- Usa `TranslateService` de `@ngx-translate/core`

## Servicios compartidos

| Servicio | Paquete | Función |
|---|---|---|
| `AuthService` | `@aisa-plataforma/shared/frontend/auth` | `login()`, `activate()` |
| `StorageService` | `@aisa-plataforma/shared/data-access-user` | Persiste JWT y datos del usuario |
| `GlobalSettingService` | `@aisa-plataforma/shared/frontend/global-setting` | Provee versión de la app |

## Versión mostrada

```typescript
versionActual = 'V' + this.globalSettingService.version;
```

## Errores manejados

| Caso | Mensaje mostrado |
|---|---|
| Credenciales incorrectas | "El nombre de usuario o la contraseña son incorrectos" |
| Sin roles válidos | "Usuario no válido" |
| Error de red | "El nombre de usuario o la contraseña son incorrectos" |
