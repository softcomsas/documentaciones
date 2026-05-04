# Módulo: JwtToken & StrongTokenAuth

## JwtToken

> **Ruta:** `source/components/JwtToken.php`
> **Propósito:** Genera y decodifica tokens JWT HS512 para sesiones de usuario.

### `encode($id_user)` → string

```php
$token = $jwt->getBuilder()
    ->setId(random_string, true)
    ->setExpiration(time() + params['jwt_expiration'])  // 3600s
    ->set('id', $id_user)
    ->sign($jwt->getSigner('HS512'), $jwt->getKey(params['jwt_secret']))
    ->getToken();
```

Payload del token:

| Claim | Valor |
|-------|-------|
| `jti` | String aleatorio (único) |
| `exp` | `time() + 3600` |
| `id` | ID del usuario |

### `decode($token)` → array \| false

Retorna `['id' => $id_user]` si el token es válido, `false` si no.

### Riesgos

- 🔴 `jwt_secret` configurado como `'my_secret_key'` en `params.php` — debe reemplazarse en producción.
- ⚠️ No hay blacklist de tokens — un token robado es válido hasta su expiración.
- ⚠️ No hay refresh token.

---

## StrongTokenAuth

> **Ruta:** `source/components/StrongTokenAuth.php`
> **Propósito:** Guard global de la aplicación para comunicación entre microservicios.

### Funcionamiento

```php
public function authenticate($user, $request, $response)
{
    $accessToken = $request->getHeaders()->get('Authorization');
    if (is_string($accessToken) && $this->validateAccessToken($accessToken)) {
        return true;
    }
    // Si hay token pero no válido → 401
    if ($accessToken !== null) {
        $this->handleFailure($response);
    }
    return null;
}

public function validateAccessToken($accessToken)
{
    return $this->seed === $accessToken; // comparación directa
}
```

### Configuración en `main.php`

```php
'as accessControl' => [
    'class' => StrongTokenAuth::class,
    'seed' => '123456',       // 🔴 hardcodeado en ejemplo
    'optional' => [
        'default/index',      // Swagger UI
        'default/api',
        'debug/*'
    ]
]
```

### Riesgos

- 🔴 El `seed` es un string estático — si se filtra, cualquiera puede llamar a todos los endpoints.
- ⚠️ La comparación no usa `hash_equals` — vulnerable a timing attacks.
- ⚠️ El seed `'123456'` del ejemplo podría estar en producción sin cambiarse.
