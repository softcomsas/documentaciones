# Funcionalidad: Consultas Transversales

## Descripción

Endpoints de consulta que cruzan las tablas de asociación para obtener entidades relacionadas.

## Roles de un usuario

```
GET /role/by-users?users_id={id_user}
Authorization: {seed}
```

Retorna los roles asignados a un usuario específico (vínculos activos).

---

## Usuarios de un rol

```
GET /user/by-roles?roles_id={id_role}
Authorization: {seed}
```

Retorna los usuarios que tienen asignado un rol específico.

---

## Permisos de un rol

```
GET /permission/by-roles?roles_id={id_role}
Authorization: {seed}
```

Retorna todos los permisos asociados a uno o varios roles.

---

## Permisos de un usuario

```
GET /permission/by-users?users_id={id_user}
Authorization: {seed}
```

Retorna todos los permisos efectivos de un usuario (a través de sus roles).

Internamente usa `RolePermission::byUsers()`:

```sql
SELECT DISTINCT rp.id_permission, ur.id_user
FROM role_permission rp
INNER JOIN user_role ur ON ur.id_role = rp.id_role
WHERE ur.id_user = :id_user
  AND rp.deleted_at IS NULL
  AND ur.deleted_at IS NULL
```

---

## Parámetro `deleted`

Todos los endpoints de listado aceptan `?deleted=1` para incluir registros eliminados:

```
GET /permission?deleted=1
```

Por defecto (`deleted=0`) solo retorna registros activos.

## Referencias

- [[modulo-role-controller]]
- [[modulo-user-controller]]
- [[modulo-permission-controller]]
