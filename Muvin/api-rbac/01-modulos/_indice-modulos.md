# Índice de Módulos — api-rbac

| # | Módulo | Tipo | Archivo | Descripción |
|---|--------|------|---------|-------------|
| 1 | [UserController](./modulo-user-controller.md) | Controller | controllers/UserController.php | CRUD usuarios + login + JWT + check-access |
| 2 | [RoleController](./modulo-role-controller.md) | Controller | controllers/RoleController.php | CRUD roles + by-users |
| 3 | [PermissionController](./modulo-permission-controller.md) | Controller | controllers/PermissionController.php | CRUD permisos + by-roles + by-users |
| 4 | [RolePermissionController](./modulo-role-permission-controller.md) | Controller | controllers/RolePermissionController.php | Vincular/desvincular roles↔permisos en lote |
| 5 | [UserRoleController](./modulo-user-role-controller.md) | Controller | controllers/UserRoleController.php | Vincular/desvincular usuarios↔roles en lote |
| 6 | [JwtToken](./modulo-jwt-token.md) | Component | components/JwtToken.php | Encode/decode JWT HS512 |
| 7 | [StrongTokenAuth](./modulo-strong-token-auth.md) | Component | components/StrongTokenAuth.php | Guard inter-servicios por seed |
| 8 | [User model](./modulo-models.md#user) | Model | models/User.php | ActiveRecord tabla user |
| 9 | [Role model](./modulo-models.md#role) | Model | models/Role.php | ActiveRecord tabla role |
| 10 | [Permission model](./modulo-models.md#permission) | Model | models/Permission.php | ActiveRecord tabla permission |
| 11 | [RolePermission model](./modulo-models.md#role-permission) | Model | models/RolePermission.php | ActiveRecord tabla role_permission + checkAccess |
| 12 | [UserRole model](./modulo-models.md#user-role) | Model | models/UserRole.php | ActiveRecord tabla user_role |
| 13 | [CheckAccessToService](./modulo-form-models.md#check-access) | Form Model | models/form/CheckAccessToService.php | Validación del check-access |
| 14 | [LoginForm](./modulo-form-models.md#login-form) | Form Model | models/form/LoginForm.php | Validación login + contraseña |
| 15 | [Migrations](./modulo-migrations.md) | DB | migrations/*.php | 5 migraciones de schema |
