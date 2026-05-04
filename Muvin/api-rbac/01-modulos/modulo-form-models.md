# Módulo: Form Models

## LoginForm {#login-form}

> **Ruta:** `source/models/form/LoginForm.php`
> **Usado por:** `UserController::actionLogin()`

### Atributos

| Campo | Tipo | Validación |
|-------|------|------------|
| `username` | string | required |
| `password` | string | required, min:6 |

### Flujo de validación

1. `username` y `password` requeridos.
2. `validatePassword` → busca usuario por `name`, verifica hash con `Yii::$app->security->validatePassword`.
3. Si falla → error `'Usuario o contraseña incorrecta'`.

### Retorno tras login exitoso

```php
return $model->getUser()->toArray(['id', 'name'], ['token']);
// { "id": 1, "name": "admin", "token": "eyJ..." }
```

---

## CheckAccessToService {#check-access}

> **Ruta:** `source/models/form/CheckAccessToService.php`
> **Usado por:** `UserController::actionCheckAccessToService()`

### Atributos

| Campo | Tipo | Validación |
|-------|------|------------|
| `id_user` | integer | required, min:1 |
| `service` | string | required, min:3, max:100 |
| `type` | string | required, in: GET/POST/PUT/DELETE |

### `checkAccess()` → bool

Delega a `RolePermission::checkAccess($id_user, $service, $type)`. Retorna `true` si el usuario tiene acceso, `false` si no.

**Ejemplo de uso desde otro microservicio:**

```json
POST /user/check-access-to-service
Authorization: 123456

{
  "id_user": 42,
  "service": "pedidos/crear",
  "type": "POST"
}
```

Respuesta: `true` o `false`

---

## ChangePasswordForm {#change-password}

> **Ruta:** `source/models/form/ChangePasswordForm.php`
> **Usado por:** `UserController::actionChangePassword()`

Permite cambiar la contraseña del usuario autenticado. Requiere contraseña actual + nueva contraseña.
