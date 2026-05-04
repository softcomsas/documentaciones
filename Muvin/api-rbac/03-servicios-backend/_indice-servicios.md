# Índice de Servicios Backend

| # | Tag | Endpoints | Archivo |
|---|-----|-----------|---------|
| 1 | Users | 10 endpoints | [endpoint-users.md](./endpoint-users.md) |
| 2 | Roles | 6 endpoints | [endpoint-roles.md](./endpoint-roles.md) |
| 3 | Permissions | 7 endpoints | [endpoint-permissions.md](./endpoint-permissions.md) |
| 4 | Role-Permission | 2 endpoints | [endpoint-role-permission.md](./endpoint-role-permission.md) |
| 5 | User-Role | 2 endpoints | [endpoint-user-role.md](./endpoint-user-role.md) |

## Autenticación

**Todos los endpoints** (excepto Swagger UI) requieren:

```
Authorization: {seed}
```

Donde `seed` es el valor estático configurado en `StrongTokenAuth` — actúa como API key entre microservicios.
