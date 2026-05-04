# Riesgos y Deuda Técnica

> **Sección:** 08-riesgos-y-deuda-tecnica
> **Proyecto:** api-rbac

## Hotspots Críticos

### 🔴 HOT-001: Seed estático `'123456'` como único control de acceso

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `source/config/main.php` + `StrongTokenAuth.php` |
| **Riesgo** | Si el seed se filtra, cualquier actor puede leer y modificar todo el sistema RBAC |
| **Severidad** | 🔴 Crítica |
| **Fix inmediato** | Cambiar seed por un valor fuerte, moverlo a variable de entorno |

### 🔴 HOT-002: JWT secret trivial por defecto

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `source/config/params.php` |
| **Riesgo** | `'my_secret_key'` permite forjar tokens JWT para cualquier usuario |
| **Severidad** | 🔴 Crítica |
| **Fix** | `jwt_secret` aleatorio de 64+ chars desde variable de entorno |

### 🔴 HOT-003: Comparación de seed sin `hash_equals`

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `source/components/StrongTokenAuth.php` |
| **Riesgo** | Timing attack — comparación con `===` filtra info sobre longitud/prefix del seed |
| **Fix** | `return hash_equals($this->seed, $accessToken);` |

---

## Deuda Técnica

| # | Item | Área | Esfuerzo | Prioridad |
|---|------|------|----------|-----------|
| DT-01 | Mover `seed` y `jwt_secret` a variables de entorno | config | Bajo | 🔴 Alta |
| DT-02 | Usar `hash_equals` en validación del seed | StrongTokenAuth | Trivial | 🔴 Alta |
| DT-03 | Implementar blacklist/revocación de tokens JWT | JwtToken | Medio | 🟡 Media |
| DT-04 | Rate limiting en `/user/login` | config / middleware | Bajo | 🟡 Media |
| DT-05 | Aumentar longitud mínima de contraseña (≥8 chars) | User model | Trivial | 🟡 Media |
| DT-06 | Agregar campo `description` a Permission y Role | migrations | Bajo | 🟢 Baja |
| DT-07 | Migrar `sizeg/yii2-jwt` a versión con `lcobucci/jwt 4.x` | JwtToken | Medio | 🟢 Baja |
| DT-08 | Reemplazar `yii2-swiftmailer` (deprecado) | composer.json | Bajo | 🟢 Baja |
| DT-09 | Tests de integración para `checkAccess` | tests/ | Medio | 🟡 Media |
| DT-10 | Documentar convención de nombres para campo `service` | — | Trivial | 🟡 Media |
| DT-11 | Logs de auditoría (quién cambió qué rol/permiso) | models | Alto | 🟢 Baja |
| DT-12 | Endpoint de logout (invalidar token) | UserController | Medio | 🟡 Media |

---

## Recomendaciones Prioritarias

### Inmediato (antes del próximo deploy)
1. **Cambiar `seed`** a valor aleatorio fuerte en variable de entorno.
2. **Cambiar `jwt_secret`** a valor aleatorio de 64+ caracteres.
3. **Fix `hash_equals`** en `StrongTokenAuth.validateAccessToken`.

### Corto plazo
4. Rate limiting en login.
5. Validar que en producción `RBAC_ENV=Production` esté configurado.
6. Documentar y acordar convención de strings para el campo `service` en toda la plataforma.

### Mediano plazo
7. Implementar revocación de JWT (Redis blacklist o DB).
8. Tests de integración para el flujo de `checkAccess`.
9. Considerar migrar a OAuth2 / API keys por microservicio para mayor granularidad.
