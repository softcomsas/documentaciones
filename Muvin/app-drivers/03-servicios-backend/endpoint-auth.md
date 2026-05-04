# Endpoints: Autenticación

> **Base URL:** `https://pruebas.muvinapp.com/api/backend/web/`

## POST `login/send-code`

Solicita el envío de un código SMS al número de teléfono indicado.

**Request:**
```json
{
  "telefono": "1155551234"
}
```

**Response exitoso:**
```json
{
  "ok": true
}
```

**Response error:**
```json
{
  "message": "Número no registrado"
}
```

---

## POST `login/telefono`

Valida el código SMS y retorna el token de sesión.

**Request:**
```json
{
  "telefono": "1155551234",
  "codigo": "123456"
}
```

**Response exitoso:**
```json
{
  "token": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response error (422):**
```json
{
  "message": "Código incorrecto"
}
```

## Notas

- No hay endpoint de logout — el token se elimina localmente borrando la preferencia.
- No se conoce el tiempo de expiración del token.
- No hay refresh token.
