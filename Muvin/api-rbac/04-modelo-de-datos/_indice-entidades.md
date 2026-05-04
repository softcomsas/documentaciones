# Índice de Entidades / Modelo de Datos

| # | Entidad | Tabla | Archivo |
|---|---------|-------|---------|
| 1 | [User](./entidad-user.md) | `user` | models/User.php |
| 2 | [Role](./entidad-role.md) | `role` | models/Role.php |
| 3 | [Permission](./entidad-permission.md) | `permission` | models/Permission.php |
| 4 | [RolePermission](./entidad-role-permission.md) | `role_permission` | models/RolePermission.php |
| 5 | [UserRole](./entidad-user-role.md) | `user_role` | models/UserRole.php |

## Diagrama ER completo

```mermaid
erDiagram
    user ||--o{ user_role : "tiene roles"
    role ||--o{ user_role : "asignado a usuarios"
    role ||--o{ role_permission : "tiene permisos"
    permission ||--o{ role_permission : "asignada a roles"

    user {
        int id PK
        string name
        string password_hash
        int created_at
        int updated_at
        int deleted_at
    }
    role {
        int id PK
        string name
        int created_at
        int updated_at
        int deleted_at
    }
    permission {
        int id PK
        string name
        string service
        string type
        int created_at
        int updated_at
        int deleted_at
    }
    user_role {
        int id PK
        int id_user FK
        int id_role FK
        int created_at
        int deleted_at
    }
    role_permission {
        int id PK
        int id_role FK
        int id_permission FK
        int created_at
        int deleted_at
    }
```

## Patrón de soft delete

Todas las tablas usan `deleted_at` (INT NULL). El valor `NULL` indica activo, un timestamp Unix indica eliminado. Los índices `UNIQUE` incluyen `deleted_at` para permitir reutilizar valores después de eliminar.
