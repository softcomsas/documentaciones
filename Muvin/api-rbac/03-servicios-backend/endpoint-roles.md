# Endpoints: Roles, Permisos, Asociaciones

## Roles

> **Tag:** Roles | **Auth:** `Authorization: {seed}`

### GET `/role`
Lista roles activos.

### GET `/role/{id}`
Un rol por ID. 404 si no existe o está eliminado.

### POST `/role`
```json
{ "name": "string (min3, max100)" }
```
- **201:** ID
- **422:** Errores (nombre duplicado, etc.)

### PUT `/role/{id}`
```json
{ "name": "string" }
```
- **204:** OK
- **405:** Si el registro está eliminado

### DELETE `/role/{id}`
Soft delete. **204** OK.

### GET `/role/by-users?users_id={id}`
Roles del usuario indicado.

---

## Permisos

> **Tag:** Permissions | **Auth:** `Authorization: {seed}`

### GET `/permission`
Lista permisos activos.

### GET `/permission/{id}`
Un permiso. 404 si no existe.

### POST `/permission`
```json
{
  "name": "string (min3, max100)",
  "service": "string (min3, max255)",
  "type": "GET|POST|PUT|DELETE"
}
```
- **201:** ID
- **422:** Si ya existe `(service, type)` activo

### PUT `/permission/{id}`
Mismos campos, todos opcionales.
- **204:** OK

### DELETE `/permission/{id}`
Soft delete. **204** OK.

### GET `/permission/by-roles?roles_id={id}`
Permisos del rol indicado.

### GET `/permission/by-users?users_id={id}`
Permisos efectivos del usuario (a través de sus roles).

---

## Role-Permission

> **Tag:** Roles | **Auth:** `Authorization: {seed}`

### POST `/role-permission`
Body: array de `{id_role, id_permission}`.
- **201:** Array de IDs creados
- **422:** Errores por item

### DELETE `/role-permission`
Body: array de `{id_role, id_permission}`.
- **204:** OK

---

## User-Role

### POST `/user-role`
Body: array de `{id_user, id_role}`.
- **201:** Array de IDs

### DELETE `/user-role`
Body: array de `{id_user, id_role}`.
- **204:** OK
