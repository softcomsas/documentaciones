# Módulo: Models

## User {#user}

> **Tabla:** `user` | **Archivo:** `models/User.php`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK AI | Identificador |
| `name` | VARCHAR(60) UNIQUE | Nombre de usuario (login) |
| `password_hash` | VARCHAR(60) | Hash bcrypt |
| `created_at` | INT | Unix timestamp |
| `updated_at` | INT | Unix timestamp |
| `deleted_at` | INT NULL | Soft delete |

**Atributo virtual:** `$password` — recibe el texto plano y se hashea en `beforeSave` con `Yii::$app->security->generatePasswordHash()`.

**Relaciones:** `hasMany(UserRole)`, `hasMany(Role) via UserRole`

**Extra field:** `token` — generado bajo demanda por `JwtToken::encode($this->id)`. Se incluye en la respuesta del login.

---

## Role {#role}

> **Tabla:** `role` | **Archivo:** `models/Role.php`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK AI | Identificador |
| `name` | VARCHAR(100) UNIQUE | Nombre del rol |
| `created_at` | INT | Unix timestamp |
| `updated_at` | INT | Unix timestamp |
| `deleted_at` | INT NULL | Soft delete |

**Relaciones:** `hasMany(RolePermission)`, `hasMany(Permission) via RolePermission`, `hasMany(UserRole)`

---

## Permission {#permission}

> **Tabla:** `permission` | **Archivo:** `models/Permission.php`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK AI | Identificador |
| `name` | VARCHAR(100) UNIQUE | Nombre descriptivo |
| `service` | VARCHAR(100) | Identificador del microservicio/endpoint |
| `type` | VARCHAR(7) | Verbo HTTP: GET \| POST \| PUT \| DELETE |
| `created_at` | INT | Unix timestamp |
| `updated_at` | INT | Unix timestamp |
| `deleted_at` | INT NULL | Soft delete |

**Unique constraint compuesto:** `(service, type, deleted_at)` — no puede haber dos permisos activos para el mismo `service+type`.

---

## RolePermission {#role-permission}

> **Tabla:** `role_permission` | **Archivo:** `models/RolePermission.php`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK AI | Identificador |
| `id_role` | INT FK → role | Rol |
| `id_permission` | INT FK → permission | Permiso |
| `created_at` | INT | Unix timestamp |
| `deleted_at` | INT NULL | Soft delete |

**Método clave:** `checkAccess($id_user, $service, $type)` — SQL con JOIN `user_role`:

```sql
SELECT EXISTS (
  SELECT 1 FROM role_permission t
  JOIN permission p ON p.id = t.id_permission
  JOIN user_role ur ON ur.id_role = t.id_role
  WHERE ur.id_user = :id_user
    AND p.service = :service
    AND p.type = :type
    AND ur.deleted_at IS NULL
    AND p.deleted_at IS NULL
    AND t.deleted_at IS NULL
)
```

**Soft delete personalizado:** `deleteInternal()` hace `UPDATE SET deleted_at = time()` en lugar de DELETE físico.

---

## UserRole {#user-role}

> **Tabla:** `user_role` | **Archivo:** `models/UserRole.php`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK AI | Identificador |
| `id_user` | INT FK → user | Usuario |
| `id_role` | INT FK → role | Rol |
| `created_at` | INT | Unix timestamp |
| `deleted_at` | INT NULL | Soft delete |

Misma lógica de soft delete que `RolePermission`.
