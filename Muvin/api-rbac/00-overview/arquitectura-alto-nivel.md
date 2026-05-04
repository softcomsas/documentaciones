# Arquitectura de Alto Nivel — api-rbac

## Diagrama de componentes

```mermaid
graph TD
    subgraph api-rbac
        UC[UserController]
        RC[RoleController]
        PC[PermissionController]
        RPC[RolePermissionController]
        URC[UserRoleController]
        DC[DefaultController\nSwagger UI]

        JWT[JwtToken\ncomponent]
        STA[StrongTokenAuth\ncomponent]

        UM[User model]
        RM[Role model]
        PM[Permission model]
        RPM[RolePermission model]
        URM[UserRole model]

        CAS[CheckAccessToService\nform model]
        LF[LoginForm\nform model]
    end

    UC --> UM
    UC --> JWT
    UC --> LF
    UC --> CAS
    RC --> RM
    PC --> PM
    RPC --> RPM
    URC --> URM
    RPM --> CAS

    STA -->|global auth filter| UC & RC & PC & RPC & URC
    DB[(MySQL)]
    UM & RM & PM & RPM & URM --> DB
```

## Routing

Las rutas se definen mediante `yii\rest\UrlRule` en `config/main.php`:

| Patrón | Controller | Verbos disponibles |
|--------|------------|--------------------|
| `/user` | UserController | GET, POST |
| `/user/{id}` | UserController | GET, PUT/PATCH, DELETE |
| `/user/login` | UserController | POST |
| `/user/decode-token` | UserController | POST |
| `/user/change-password` | UserController | PUT |
| `/user/check-access-to-service` | UserController | POST |
| `/user/by-roles` | UserController | GET |
| `/role` | RoleController | GET, POST |
| `/role/{id}` | RoleController | GET, PUT/PATCH, DELETE |
| `/role/by-users` | RoleController | GET |
| `/permission` | PermissionController | GET, POST |
| `/permission/{id}` | PermissionController | GET, PUT/PATCH, DELETE |
| `/permission/by-roles` | PermissionController | GET |
| `/permission/by-users` | PermissionController | GET |
| `/role-permission` | RolePermissionController | POST, DELETE |
| `/user-role` | UserRoleController | POST, DELETE |
| `/` | DefaultController | GET (Swagger UI) |

## Modelo de datos — relaciones

```mermaid
erDiagram
    user {
        int id PK
        string name UK
        string password_hash
        int created_at
        int updated_at
        int deleted_at
    }
    role {
        int id PK
        string name UK
        int created_at
        int updated_at
        int deleted_at
    }
    permission {
        int id PK
        string name UK
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

    user ||--o{ user_role : "tiene"
    role ||--o{ user_role : "asignado a"
    role ||--o{ role_permission : "tiene"
    permission ||--o{ role_permission : "asignada a"
```
