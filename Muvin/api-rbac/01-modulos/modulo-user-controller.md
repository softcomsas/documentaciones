# Módulo: UserController

> **Ruta:** `source/controllers/UserController.php`
> **Extiende:** `yii\rest\Controller`
> **Criticidad:** 🔴 Alta — contiene login, JWT y check-access

## Acciones

| Acción | Verbo | Ruta | Auth | Descripción |
|--------|-------|------|------|-------------|
| `actionIndex` | GET | `/user` | ✅ seed | Lista todos los usuarios (sin deleted) |
| `actionView` | GET | `/user/{id}` | ✅ seed | Un usuario por ID |
| `actionCreate` | POST | `/user` | ✅ seed | Crear usuario |
| `actionUpdate` | PUT | `/user/{id}` | ✅ seed | Actualizar usuario |
| `actionDelete` | DELETE | `/user/{id}` | ✅ seed | Soft-delete usuario |
| `actionLogin` | POST | `/user/login` | ✅ seed | Login → retorna JWT |
| `actionDecodeToken` | POST | `/user/decode-token` | ✅ seed | Decodifica token → id_user |
| `actionChangePassword` | PUT | `/user/change-password` | ✅ seed | Cambiar contraseña |
| `actionByRoles` | GET | `/user/by-roles?roles_id=X` | ✅ seed | Usuarios por rol |
| `actionCheckAccessToService` | POST | `/user/check-access-to-service` | ✅ seed | Verificar acceso a servicio |

## Acción crítica: `actionLogin`

```php
public function actionLogin()
{
    $model = new \app\models\form\LoginForm();
    $model->load(Yii::$app->getRequest()->getBodyParams(), '');
    if ($model->validate()) {
        return $model->getUser()->toArray(['id', 'name'], ['token']);
    }
    return $model; // errores de validación
}
```

Retorna: `{ id, name, token }` donde `token` es JWT HS512.

## Acción crítica: `actionCheckAccessToService`

```php
public function actionCheckAccessToService()
{
    $model = new \app\models\form\CheckAccessToService();
    $model->load(Yii::$app->getRequest()->getBodyParams(), '');
    if (!$model->validate()) return $model;
    return $model->checkAccess(); // bool
}
```

## findModel

```php
public function findModel($id)
{
    $model = $this->modelClass::findOne($id);
    if (!isset($model) || $model->deleted_at)
        throw new NotFoundHttpException("No existe la fila: $id");
    return $model;
}
```

Respeta soft delete: registros con `deleted_at != null` se tratan como inexistentes.

## Dependencias

- [[modulo-jwt-token]] — login + decode
- [[modulo-form-models#login-form]] — validación de credenciales
- [[modulo-form-models#check-access]] — verificación de acceso
- [[modulo-models#user]] — ActiveRecord
