# Árbol de Estructura de Archivos

> **Raíz:** `api-rbac/`

```
api-rbac/
├── .example.env                        # Variables de entorno de ejemplo
├── .gitlab-ci.yml                      # CI/CD GitLab
├── docker-compose.yaml                 # rbac + db (MySQL) + phpmyadmin
├── Dockerfile                          # PHP + Apache
├── README.md
├── bin/
│   └── build                           # Script de build
├── config/
│   ├── 000-default.conf                # Apache VirtualHost
│   └── main-local-example.php          # Configuración local de ejemplo
└── source/
    ├── composer.json                   # Dependencias PHP
    ├── codeception.yml                 # Config de tests
    ├── yii / yii.bat                   # Entry point CLI
    ├── api/                            # Web root (index.php, .htaccess)
    ├── assets/
    │   └── AppAsset.php
    ├── commands/
    │   └── HelloController.php         # Comando CLI de ejemplo
    ├── components/
    │   ├── JwtToken.php                # 🔑 Encode/decode JWT HS512
    │   └── StrongTokenAuth.php         # 🔒 Guard inter-servicios
    ├── config/
    │   ├── main.php                    # Config principal (routing, auth, DB)
    │   ├── params.php                  # jwt_secret, jwt_expiration
    │   ├── console.php                 # Config CLI
    │   ├── swagger.php                 # Config Swagger
    │   ├── test.php / test_db.php      # Config tests
    │   └── __autocomplete.php
    ├── controllers/
    │   ├── DefaultController.php       # Swagger UI
    │   ├── PermissionController.php    # CRUD permisos + by-roles/users
    │   ├── RoleController.php          # CRUD roles + by-users
    │   ├── RolePermissionController.php # Vincular roles↔permisos
    │   ├── UserController.php          # CRUD + login + check-access
    │   └── UserRoleController.php      # Vincular usuarios↔roles
    ├── environments/
    │   ├── dev/                        # Config local dev
    │   └── prod/                       # Config producción
    ├── migrations/
    │   ├── m220615_210841_init.php
    │   ├── m220630_143923_relations_m_n.php
    │   ├── m220630_195347_standar_columns.php
    │   ├── m221221_213926_alter_columns_.php
    │   └── m222783_101221_alter_name_permission_...php
    ├── models/
    │   ├── Permission.php + PermissionQuery.php
    │   ├── PermissionsByRoles.php      # Consulta transversal
    │   ├── PermissionsByUsers.php      # Consulta transversal
    │   ├── Role.php + RoleQuery.php
    │   ├── RolePermission.php + RolePermissionQuery.php
    │   ├── RolePermissionsLot.php      # Operaciones en lote
    │   ├── RolesByUsers.php            # Consulta transversal
    │   ├── User.php + UserQuery.php + UserIdentity.php
    │   ├── UserRole.php + UserRoleQuery.php
    │   ├── UserRolesLot.php            # Operaciones en lote
    │   ├── UsersByRoles.php            # Consulta transversal
    │   └── form/
    │       ├── ChangePasswordForm.php
    │       ├── CheckAccessToService.php
    │       └── LoginForm.php
    └── tests/                          # Tests Codeception
        ├── acceptance/
        ├── functional/
        └── unit/
```

## Estadísticas

| Categoría | Cantidad |
|-----------|----------|
| Controllers | 6 |
| Models ActiveRecord | 5 |
| Models Query | 4 |
| Models Lot (lote) | 2 |
| Models transversales | 4 |
| Form models | 3 |
| Migrations | 5 |
| Components custom | 2 |
