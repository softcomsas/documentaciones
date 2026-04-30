# Endpoint: Auth

> [[_indice-servicios]] | Módulo: [[modulo-login]]

## POST `/login/login`

**Auth:** ❌ Público

**Request:**
```json
{ "username": "admin", "password": "contraseña" }
```

**Response 200:**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "access_token": "a1b2c3d4e5f6...",
    "authorization_code": "auth_key_32chars",
    "expires_at": "2024-01-15 18:30:00"
  }
}
```

**Response 425 (credenciales inválidas):**
```json
{
  "success": false,
  "status": 425,
  "data": { "password": ["Incorrect password."] }
}
```

**Notas:**
- Token generado con `md5(uniqid())`. TTL = 9 horas desde generación.
- Si el token vigente no ha expirado, se reutiliza (no se genera uno nuevo).
