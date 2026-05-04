# Módulo: RolePermissionController & UserRoleController

> **Rutas:** `source/controllers/RolePermissionController.php`, `source/controllers/UserRoleController.php`
> **Propósito:** Operaciones en lote para vincular/desvincular entidades

## RolePermissionController — Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionCreate` | POST | `/role-permission` | Vincula rol↔permiso (array en body) |
| `actionDelete` | DELETE | `/role-permission` | Desvincula rol↔permiso (array en body) |

## Operación en lote

El body es un **array** de objetos `{id_role, id_permission}`:

```json
[
  { "id_role": 1, "id_permission": 5 },
  { "id_role": 1, "id_permission": 7 }
]
```

Internamente carga cada item como `RolePermissionsLot` con el scenario correspondiente (`SCENARIO_CREATE` / `SCENARIO_DELETE`), valida con `Model::validateMultiple`, y ejecuta el save/delete de cada uno.

## UserRoleController — Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionCreate` | POST | `/user-role` | Vincula usuario↔rol (array en body) |
| `actionDelete` | DELETE | `/user-role` | Desvincula usuario↔rol (array en body) |

Misma lógica que `RolePermissionController` pero con `UserRolesLot`.

## Manejo de errores en lote

Si algún item del array falla validación:
- HTTP 422
- Retorna array con todos los errores de todos los modelos del lote

## Dependencias

- [[modulo-models#role-permission]]
- [[modulo-models#user-role]]
