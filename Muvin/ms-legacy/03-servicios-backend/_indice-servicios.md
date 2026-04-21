# Índice de servicios backend

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Servicios externos consumidos

| Dominio | Descripción | Endpoints documentados | Estado | Detalle |
|---------|-------------|----------------------|--------|---------|
| `persona-rol` | Entidades de compradores en el sistema legacy | 1 activo, 1 declarado | 🟡 Parcial | [[persona-rol-endpoints]] |

## URL base del backend legacy

```
https://dev.muvinapp.com/api/backend/web/
```

> [!warning] URL de desarrollo hardcodeada como default
> La URL en el `docker-compose.yml` usa `dev.muvinapp.com` como valor por defecto. Confirmar que en producción se sobreescribe correctamente con `LEGACY_MICROSERVICE_BASE_URL`. Ver [[security-inventory]].

## Autenticación hacia el backend legacy

> ⚠️ **Pendiente de verificar.** No se observa ningún header de autenticación (Bearer token, API Key, Basic Auth) en las llamadas HTTP de `AppService`. Determinar si el backend legacy requiere autenticación y cómo se gestiona.
