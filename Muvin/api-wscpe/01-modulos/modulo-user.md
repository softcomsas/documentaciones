# Módulo: UserController

> **Archivo:** `controllers/UserController.php`
> **Ruta base:** `/user`
> **Auth:** ✅ Bearer token + verificación `username == 'admin'`
> **Criticidad:** 🟡 Media

## Propósito

Permite crear nuevos usuarios de la API. Solo accesible por `admin`. No expone listado ni eliminación de usuarios.

## Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionCreateUser` | POST | `/user/create-user` | Crear un usuario nuevo |
| `actionIndex` | ANY | `/user` | Lanza 404 |

## actionCreateUser — Detalle

**Request:**
```json
{
  "username": "nuevo_usuario",
  "password": "contraseña_segura",
  "email": "usuario@example.com"
}
```

**Lógica:**
1. Verifica que el solicitante sea `admin`.
2. Crea `SignupForm`, carga parámetros, valida y guarda.
3. El password es hasheado con `password_hash()` (bcrypt) por `SignupForm`.

**Response exitosa:**
```json
{
  "success": true,
  "status": 200,
  "data": "Usuario creado éxitosamente."
}
```

## Módulo: SwaggerController

> **Ruta:** `/swagger` · Auth: ❌ Público

Sirve la documentación interactiva Swagger UI generada a partir de las anotaciones `@SWG\*` en los controllers. Usa el paquete `light/yii2-swagger`.

Útil para explorar y probar los endpoints en desarrollo. En producción debería estar protegido o deshabilitado.
