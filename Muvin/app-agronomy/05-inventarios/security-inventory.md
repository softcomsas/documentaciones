# Inventario de Seguridad — App Agronomy

> **Última revisión:** 2026-04-30

## Resumen ejecutivo

| Severidad | Cantidad |
|-----------|---------|
| 🔴 Crítico | 4 |
| 🟡 Medio | 5 |
| 🟢 Bajo | 2 |

---

## Hallazgos críticos 🔴

### [SEC-01] Token almacenado en `localStorage`
- **Severidad:** 🔴 Crítico
- **Descripción:** El token de autenticación se guarda en `localStorage`. Esto lo expone a robo mediante ataques XSS. La alternativa segura es usar cookies `httpOnly`.
- **Ubicación:** `src/app/pages/sessions/services/auth.service.ts` (método `setSession`), `src/app/shared/guards/centro-auth.guard.ts`
- **Recomendación:** Migrar a cookies `httpOnly` + `Secure` + `SameSite=Strict`.

### [SEC-02] Expiración de token desactivada en el Guard
- **Severidad:** 🔴 Crítico
- **Descripción:** La validación de expiración del token (`if (dateExpires < now)`) está comentada en `CentroAuthGuard`. Un token expirado sigue siendo válido mientras esté en `localStorage`.
- **Ubicación:** `src/app/shared/guards/centro-auth.guard.ts:14-18`
- **Recomendación:** Descomentar y activar la validación de expiración.

### [SEC-03] Bug de asignación en comparación de status (login)
- **Severidad:** 🔴 Crítico
- **Descripción:** `if (res.status = 1)` es una asignación, no una comparación. El bloque `setSession` se ejecuta siempre, incluso ante respuestas de error del servidor. Permite que usuarios con credenciales inválidas puedan obtener una sesión parcial.
- **Ubicación:** `src/app/pages/sessions/services/auth.service.ts:36`, `:55`
- **Recomendación:** Cambiar a `if (res.status === 1)`.

### [SEC-04] `console.log` con datos sensibles en producción
- **Severidad:** 🔴 Crítico
- **Descripción:** `ChoferService.addChofer()` hace `console.log(chofer)` antes del POST. Expone datos de personas (nombre, CUIT, etc.) en la consola del browser en producción.
- **Ubicación:** `src/app/pages/transportistas/services/chofer.service.ts:18`
- **Recomendación:** Eliminar el `console.log`. Usar un servicio de logging que respete el ambiente.

---

## Hallazgos medios 🟡

### [SEC-05] Código de debug en producción (`prueba()`)
- **Severidad:** 🟡 Medio
- **Descripción:** `AuthService` tiene un método `prueba()` que hace GET a `/prueba` con el token en el header. Parece ser código de testing. No debe estar en producción.
- **Ubicación:** `src/app/pages/sessions/services/auth.service.ts`
- **Recomendación:** Eliminar el método.

### [SEC-06] Headers de autenticación construidos estáticamente
- **Severidad:** 🟡 Medio
- **Descripción:** Los headers en `AuthService` se construyen en la inicialización de la clase (`const headers = new HttpHeaders({... localStorage.getItem('token')})`). Si el token no existe al momento de carga del módulo, el header queda vacío.
- **Ubicación:** `src/app/pages/sessions/services/auth.service.ts:11-14`
- **Recomendación:** Usar un interceptor HTTP que inyecte el token dinámicamente en cada petición.

### [SEC-07] Construcción manual de URLs (potencial Open Redirect)
- **Severidad:** 🟡 Medio
- **Descripción:** Varios métodos en `reservas.service.ts` construyen URLs por concatenación de strings directamente desde parámetros de entrada. Aunque el backend valida los datos, es una práctica riesgosa.
- **Ubicación:** `src/app/pages/pedidos/services/reservas.service.ts` (múltiples métodos)
- **Recomendación:** Usar `HttpParams` de Angular en todos los casos.

### [SEC-08] `ng-recaptcha` desactualizado
- **Severidad:** 🟡 Medio
- **Descripción:** Versión `12.0.2` (para Angular 12) usada con Angular 16. Puede tener vulnerabilidades conocidas.
- **Recomendación:** Actualizar a la versión compatible con Angular 16.

### [SEC-09] `angular-in-memory-web-api` presente en dependencias
- **Severidad:** 🟡 Medio
- **Descripción:** `angular-in-memory-web-api@0.16.0` está en las dependencias de producción (no en `devDependencies`). Si está activo, intercepta peticiones HTTP reales y las responde con datos falsos.
- **Ubicación:** `package.json`
- **Recomendación:** Verificar si está activo. Si solo se usa en desarrollo, mover a `devDependencies` o eliminar.

---

## Hallazgos bajos 🟢

### [SEC-10] Sin HTTPS forzado visible en Nginx
- **Severidad:** 🟢 Bajo
- **Descripción:** `nginx/default.conf` no tiene configuración visible de redirección HTTP → HTTPS. Verificar en la infraestructura que HTTPS sea forzado.
- **Recomendación:** 🚧 Pendiente de verificar en el entorno de despliegue.

### [SEC-11] Roles numéricos hardcodeados
- **Severidad:** 🟢 Bajo
- **Descripción:** Los roles (`3`, `11`, `16`) están hardcodeados en el guard y en servicios. Si los IDs de roles cambian en el backend, requiere cambio de código.
- **Recomendación:** Centralizar en una constante o enum (`RolUsuario`).
