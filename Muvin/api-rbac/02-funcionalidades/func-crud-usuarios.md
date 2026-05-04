# Funcionalidad: CRUD Entidades

## CRUD Usuarios {#crud-usuarios}

> **Ruta base:** `/user`

| Operación | Request | Response |
|-----------|---------|----------|
| Listar | `GET /user` | Array de User (sin deleted) |
| Ver uno | `GET /user/{id}` | User object |
| Crear | `POST /user` body: `{name, password}` | `id` del nuevo usuario (201) |
| Actualizar | `PUT /user/{id}` body: `{name?, password?}` | 204 No Content |
| Eliminar | `DELETE /user/{id}` | 204 No Content (soft delete) |
| Cambiar clave | `PUT /user/change-password` body: ver schema | 204 No Content |

**Validaciones de usuario:**
- `name`: min 3, max 50, único entre activos
- `password`: min 6, max 50, requerido solo en creación

---

## CRUD Roles {#crud-roles}

> **Ruta base:** `/role`

| Operación | Request | Response |
|-----------|---------|----------|
| Listar | `GET /role` | Array de Role |
| Ver uno | `GET /role/{id}` | Role object |
| Crear | `POST /role` body: `{name}` | `id` (201) |
| Actualizar | `PUT /role/{id}` body: `{name}` | 204 |
| Eliminar | `DELETE /role/{id}` | 204 (soft delete) |

**Validaciones de rol:**
- `name`: min 3, max 100, único entre activos

---

## CRUD Permisos {#crud-permisos}

> **Ruta base:** `/permission`

| Operación | Request | Response |
|-----------|---------|----------|
| Listar | `GET /permission` | Array de Permission |
| Ver uno | `GET /permission/{id}` | Permission object |
| Crear | `POST /permission` body: `{name, service, type}` | `id` (201) |
| Actualizar | `PUT /permission/{id}` body: `{name?, service?, type?}` | 204 |
| Eliminar | `DELETE /permission/{id}` | 204 (soft delete) |

**Validaciones de permiso:**
- `name`: min 3, max 100, único entre activos
- `service`: min 3, max 255
- `type`: enum GET | POST | PUT | DELETE
- Unique compuesto: `(service, type)` entre activos — no puede haber dos permisos para el mismo servicio+verbo

---

## Comportamiento de soft delete

En **todos los controllers**, `findModel($id)` lanza 404 si el registro tiene `deleted_at != null`. El `actionDelete` hace soft delete (`deleted_at = time()`), no borrado físico.

Si se intenta modificar un registro eliminado → HTTP 405 Method Not Allowed.
