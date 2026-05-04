# Módulo: RoleController & PermissionController

> **Rutas:** `source/controllers/RoleController.php`, `source/controllers/PermissionController.php`
> **Extiende:** `yii\rest\Controller`

## RoleController — Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionIndex` | GET | `/role` | Lista todos los roles activos |
| `actionView` | GET | `/role/{id}` | Un rol por ID |
| `actionCreate` | POST | `/role` | Crear rol |
| `actionUpdate` | PUT | `/role/{id}` | Actualizar rol |
| `actionDelete` | DELETE | `/role/{id}` | Soft-delete rol |
| `actionByUsers` | GET | `/role/by-users?users_id=X` | Roles de un usuario |

## PermissionController — Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionIndex` | GET | `/permission` | Lista todos los permisos activos |
| `actionView` | GET | `/permission/{id}` | Un permiso por ID |
| `actionCreate` | POST | `/permission` | Crear permiso |
| `actionUpdate` | PUT | `/permission/{id}` | Actualizar permiso |
| `actionDelete` | DELETE | `/permission/{id}` | Soft-delete permiso |
| `actionByRoles` | GET | `/permission/by-roles?roles_id=X` | Permisos de un/varios roles |
| `actionByUsers` | GET | `/permission/by-users?users_id=X` | Permisos de un/varios usuarios |

## Patrón común (ambos controllers)

```php
public function actionIndex()
{
    $params = array_merge(['deleted' => 0], Yii::$app->request->queryParams);
    return $this->modelClass::selectAll($params);
    // Por defecto: solo registros no eliminados
}
```

Los parámetros de query se pasan al método `selectAll` del modelo, que filtra por columnas definidas.

## Dependencias

- [[modulo-models#role]]
- [[modulo-models#permission]]
- [[modulo-models#role-permission]] — para by-roles/by-users en PermissionController
