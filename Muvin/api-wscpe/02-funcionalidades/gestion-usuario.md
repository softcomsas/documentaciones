# F-05 · Creación de Usuario API

> **Módulo:** [modulo-user](../01-modulos/modulo-user.md)
> **Endpoint:** `POST /user/create-user`

## Descripción

Permite al `admin` crear cuentas adicionales de acceso a la API. Los usuarios creados pueden usar `POST /cpe` y `POST /certificado` según sus permisos.

## Notas

- Solo `admin` puede crear usuarios.
- El password se almacena hasheado con bcrypt.
- No hay endpoint de listado, edición ni eliminación de usuarios. La gestión de usuarios fuera de la creación debe hacerse directamente en BD.

# F-06 · Sanitización de Inputs

> **Componente:** `config/web.php` → hook `beforeRequest`

## Descripción

Todos los parámetros GET y POST son sanitizados automáticamente mediante `HtmlPurifier::process()` antes de llegar a cualquier controller. Esto protege contra XSS en los inputs de texto.

```php
function purificar($value) {
    if (is_string($value)) return HtmlPurifier::process($value);
    if (is_array($value)) { /* recursivo */ }
    return $value;
}
```

## Limitación

`HtmlPurifier` está diseñado para HTML. Para campos como `ctg` (numérico) o `cuit` (numérico), no añade protección adicional real. No reemplaza la validación de tipos en los modelos.

# F-07 · Documentación Swagger UI

> **Endpoint:** `GET /swagger`
> **Auth:** ❌ Público

Interfaz Swagger UI generada automáticamente desde las anotaciones `@SWG\*` en los controllers. Permite explorar y probar los endpoints desde el navegador.

> ⚠️ En producción, este endpoint debería estar protegido por autenticación básica o deshabilitado para evitar exponer la estructura interna de la API.
