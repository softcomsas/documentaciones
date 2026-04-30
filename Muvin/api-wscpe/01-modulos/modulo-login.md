# Módulo: LoginController

> **Archivo:** `controllers/LoginController.php`
> **Ruta base:** `/login`
> **Auth:** ❌ Público (sin `authenticator`)
> **Criticidad:** 🔴 Alta

## Propósito

Gestiona la autenticación de usuarios que consumen la API. Genera y devuelve un `access_token` MD5 con expiración de 9 horas. Es el único controller que no requiere autenticación previa.

## Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionLogin` | POST | `/login/login` | Autentica usuario y devuelve token |
| `actionIndex` | ANY | `/login` | Lanza HTTP 418 (Easter egg) |

## actionLogin — Detalle

**Request:**
```json
{ "username": "admin", "password": "..." }
```

**Lógica:**
1. Carga el `LoginForm` con los parámetros del body.
2. Valida credenciales contra la tabla `user` (password hash bcrypt).
3. Si el token existente no ha expirado → lo reutiliza.
4. Si expiró o no existe → genera nuevo: `md5(uniqid())`, TTL = 9 horas.
5. Guarda token en BD y devuelve la respuesta.

**Response exitosa (200):**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "access_token": "a1b2c3d4e5f6...",
    "authorization_code": "auth_key_value",
    "expires_at": "2024-01-15 18:30:00"
  }
}
```

**Response error (425):**
```json
{
  "success": false,
  "status": 425,
  "data": { "password": ["..."] }
}
```

## Riesgos

- 🔴 Token generado con `md5(uniqid())`. MD5 no es criptográficamente seguro. Ver [SEC-01](../05-inventarios/security-inventory.md).
- ⚠️ Sin rate limiting en el endpoint de login. Vulnerable a fuerza bruta.
- ⚠️ TTL de 9 horas es largo para un microservicio de acceso externo.
