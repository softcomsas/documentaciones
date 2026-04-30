# Endpoints de Autenticación

> **Archivo fuente:** `src/app/pages/sessions/services/auth.service.ts`
> **Base URL:** `environment.apiURL`

---

## `POST login/login-panel`

**Propósito:** Autenticar usuario con credenciales + reCAPTCHA. Detecta canal (pwa/desktop).
**Autenticación:** Ninguna (endpoint público)
**Consumido por:** [[sessions-signin]], [[modulo-sessions]]

### Payload

```json
{
  "username": "string (CUIT o email)",
  "password": "string",
  "captcha": "string (token reCAPTCHA)",
  "Canal": "pwa | desktop"
}
```

### Respuestas

| Código | Significado | Ejemplo |
|--------|-------------|---------|
| 200 | Login exitoso | `{status: 1, data: {token, rol, ...}}` |
| 401/422 | Credenciales inválidas | `{status: 0, message: "..."}` |

### Efectos secundarios

- Genera sesión en el backend.
- ⚠️ El frontend no valida correctamente `res.status` (bug: asignación en lugar de comparación).

---

## `POST login/access-token`

**Propósito:** Obtener o renovar el access token de sesión.
**Autenticación:** ⚠️ Pendiente de verificar
**Consumido por:** [[modulo-sessions]] (`activate()` en `AuthService`)

### Payload

```json
{ "⚠️ Pendiente de verificar": "estructura exacta del postData" }
```

### Respuestas

| Código | Significado | Ejemplo |
|--------|-------------|---------|
| 200 | Token generado | `{status: 1, data: {access_token, tipo_turneada, linea_whats_app, contratoRequerido}}` |

### Efectos secundarios

- Escribe en `localStorage`: `token`, `currentUser`, `tipo_turneada`, `linea_whats_app`, `contratoRequerido`.
