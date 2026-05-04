# Funcionalidad: Vincular Roles↔Permisos y Usuarios↔Roles

## Vincular Roles con Permisos

> **Endpoint:** `/role-permission`

### Crear vínculo (POST)

Asocia uno o varios pares `{id_role, id_permission}` en una sola llamada.

**Request:**
```json
POST /role-permission
Authorization: {seed}

[
  { "id_role": 1, "id_permission": 3 },
  { "id_role": 1, "id_permission": 7 }
]
```

**Response exitoso:** HTTP 201, array de IDs creados.

**Response error:** HTTP 422 con errores de validación por item.

### Eliminar vínculo (DELETE)

**Request:** mismo shape que POST.

**Response exitoso:** HTTP 204.

---

## Vincular Usuarios con Roles

> **Endpoint:** `/user-role`

### Crear vínculo (POST)

**Request:**
```json
POST /user-role
Authorization: {seed}

[
  { "id_user": 5, "id_role": 2 }
]
```

**Response exitoso:** HTTP 201.

### Eliminar vínculo (DELETE)

**Request:** mismo shape que POST.

---

## Notas

- Ambos endpoints reciben **arrays** — operación en lote.
- Validación múltiple: `Model::validateMultiple($models)` — si algún item falla, se retorna 422 con todos los errores.
- El vínculo usa soft delete: al "eliminar", se marca `deleted_at`. Se puede re-crear el mismo vínculo después.
- Constraint único en `(id_role, id_permission, deleted_at)` — no pueden existir dos vínculos activos iguales.

## Referencias

- [[modulo-role-permission-controller]]
- [[modulo-models#role-permission]]
- [[modulo-models#user-role]]
