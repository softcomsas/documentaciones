# Funcionalidad: Login de Usuario

## Descripción

Autentica un usuario por `username` + `password` y retorna un JWT HS512.

## Flujo

```mermaid
sequenceDiagram
    actor Client
    participant UC as UserController
    participant LF as LoginForm
    participant User
    participant JWT as JwtToken

    Client->>UC: POST /user/login\n{username, password}
    UC->>LF: load + validate
    LF->>User: selectOne({name: username})
    User-->>LF: User model
    LF->>LF: validatePassword (bcrypt)
    alt Válido
        LF-->>UC: user model
        UC->>JWT: encode(user.id)
        JWT-->>UC: token string
        UC-->>Client: {id, name, token}
    else Inválido
        LF-->>UC: errores
        UC-->>Client: HTTP 422 {errors}
    end
```

## Request

```json
POST /user/login
Authorization: {seed}
Content-Type: application/json

{
  "username": "admin",
  "password": "secreto123"
}
```

## Response exitoso (200)

```json
{
  "id": 1,
  "name": "admin",
  "token": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9..."
}
```

## Response error (422)

```json
[
  { "field": "password", "message": "Usuario o contraseña incorrecta" }
]
```

## Notas

- El token expira en **3600 segundos** (configurable en `params.php`).
- No hay endpoint de refresh — el usuario debe hacer login de nuevo.
- El endpoint requiere el header `Authorization: {seed}` de inter-servicios (no el JWT de usuario).

## Referencias

- [[modulo-user-controller]]
- [[modulo-form-models#login-form]]
- [[modulo-jwt-token]]
