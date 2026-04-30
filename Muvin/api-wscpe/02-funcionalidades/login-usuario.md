# F-01 · Login de Usuario API

> **Módulo:** [modulo-login](../01-modulos/modulo-login.md)
> **Endpoint:** `POST /login/login`

## Descripción

Autentica un usuario contra la BD local y devuelve un `access_token` para usar en los endpoints protegidos. El token tiene validez de 9 horas.

## Uso típico

```bash
curl -X POST https://api-wscpe.muvinapp.com/login/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"mi_password"}'
```

El `access_token` recibido se envía en subsiguientes requests como:
```
Authorization: Bearer a1b2c3d4e5f6...
```

Ver detalles en [modulo-login](../01-modulos/modulo-login.md).
