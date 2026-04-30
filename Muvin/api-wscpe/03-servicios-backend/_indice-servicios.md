# Índice de Endpoints — api-wscpe

> [[README]] · [[_indice-modulos]]

## Base URL

```
https://<host>/
```

Headers requeridos en endpoints protegidos:
```
Authorization: Bearer {access_token}
Content-Type: application/json
```

## Tabla de endpoints

| Verbo | Ruta | Auth | Descripción | Detalle |
|-------|------|------|-------------|---------|
| POST | `/login/login` | ❌ | Autenticar usuario | [auth-endpoints.md](./auth-endpoints.md) |
| POST | `/cpe` | ✅ Bearer | Consultar CPE por CTG+CUIT | [cpe-endpoints.md](./cpe-endpoints.md) |
| POST | `/certificado` | ✅ Bearer + admin | Cargar/reemplazar certificado AFIP | [certificado-endpoints.md](./certificado-endpoints.md) |
| POST | `/user/create-user` | ✅ Bearer + admin | Crear usuario | [user-endpoints.md](./user-endpoints.md) |
| GET | `/swagger` | ❌ | Swagger UI | — |

## Formato de respuesta estándar

Todas las respuestas (incluso errores) tienen el mismo wrapper:

```json
{
  "success": true | false,
  "status": 200 | 4xx | 5xx,
  "data": { ... } | [ ... ] | "mensaje"
}
```

> Excepción: `/swagger` devuelve HTML.
