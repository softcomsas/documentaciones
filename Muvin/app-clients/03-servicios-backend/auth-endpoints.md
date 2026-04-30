# Endpoints de Autenticación

> [[_indice-servicios]] | Módulo: [[modulo-auth]]

## POST `/login`

Autentica al usuario y devuelve tokens de sesión.

**Headers:**
```
X-Api-Key: uH9hatw8G3lQls7fE3
Content-Type: application/json
```

**Request:**
```json
{
  "email": "usuario@example.com",
  "password": "contraseña",
  "captcha": "HARDCODED_STRING"
}
```

> 🔴 El campo `captcha` está hardcodeado en `MuvinProvider.login()`. Siempre envía el mismo string.

**Response exitosa (200):**
```json
{
  "access_token": "eyJ...",
  "refresh_token": "eyJ...",
  "user": {
    "id": 42,
    "name": "Juan Pérez",
    "email": "usuario@example.com",
    "esDadorCupo": true,
    "esClienteFinal": false
  }
}
```

**Response de error (401):**
```json
{
  "message": "Credenciales inválidas"
}
```

**BLoC que lo consume:** `LoginBloc.login()`

---

## Notas sobre refresh de token

No existe endpoint de refresh de token implementado en la app. Si el `access_token` expira, el usuario debe hacer login nuevamente. El `refresh_token` se guarda en `SharedPreferences` pero no se usa en ningún flujo actualmente.
