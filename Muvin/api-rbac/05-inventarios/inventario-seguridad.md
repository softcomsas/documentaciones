# Inventario de Seguridad

## Hallazgos Críticos

### 🔴 SEC-001: Seed estático como API key

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `source/config/main.php` |
| **Hallazgo** | `'seed' => '123456'` — string estático como único control de acceso inter-servicios |
| **Impacto** | Cualquier proceso con acceso a la red puede llamar a **todos** los endpoints incluyendo operaciones destructivas |
| **Severidad** | 🔴 Crítica |
| **Fix** | Usar un secret fuerte aleatorio, rotarlo periódicamente, o migrar a OAuth2/mTLS |

### 🔴 SEC-002: Comparación de token sin `hash_equals`

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `source/components/StrongTokenAuth.php` |
| **Hallazgo** | `return $this->seed === $accessToken;` — comparación con `===` es vulnerable a timing attacks |
| **Fix** | `return hash_equals($this->seed, $accessToken);` |

### 🔴 SEC-003: JWT secret inseguro por defecto

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `source/config/params.php` |
| **Hallazgo** | `'jwt_secret' => 'my_secret_key'` — secret trivial |
| **Impacto** | Cualquiera puede forjar tokens JWT válidos |
| **Fix** | Usar un secret de al menos 64 caracteres aleatorios, almacenado en variable de entorno |

### ⚠️ SEC-004: Sin blacklist de tokens JWT

Los tokens JWT son válidos hasta su expiración (3600s). No hay mecanismo de invalidación ante logout o compromiso de credenciales.

### ⚠️ SEC-005: Sin rate limiting

No hay límite de intentos en `/user/login` — posible ataque de fuerza bruta.

### ⚠️ SEC-006: Contraseñas sin longitud mínima fuerte

El modelo `User` acepta contraseñas de mínimo 6 caracteres — insuficiente para un sistema de gestión de accesos.

## Aspectos positivos

- ✅ Contraseñas hasheadas con bcrypt (`Yii::$app->security->generatePasswordHash`).
- ✅ Sanitización de inputs en `beforeRequest` (función `purificar` en todos los params GET y POST).
- ✅ CSRF deshabilitado correctamente para API REST.
- ✅ Sesiones deshabilitadas (`enableSession => false`).
- ✅ Soft delete evita pérdida accidental de datos.
