# Inventario de Seguridad

> **Última revisión:** 2026-04-21
> **Ver también:** [[modulo-auth]], [[deuda-tecnica]], [[recomendaciones-modernizacion]]

---

## Resumen ejecutivo

| Severidad | Cantidad | Acción requerida |
|-----------|----------|-----------------|
| 🔴 Crítico | 3 | Inmediata |
| 🟠 Alto | 4 | Próximo sprint |
| 🟡 Medio | 4 | Backlog priorizado |
| 🟢 Bajo | 2 | Monitorear |

---

## 🔴 Críticos

### SEC-001 — Credencial hardcodeada en params.php

**Archivo:** `params.php`
**Hallazgo:**
```php
's-roles-permisos.token' => '123456',
```
**Riesgo:** Token de microservicio RBAC con valor trivial `123456`. Si este archivo está en el repositorio, cualquier persona con acceso al repo puede autenticarse contra el MS de roles/permisos.

**Acción:** Rotar el token inmediatamente. Moverlo a `params-local.php` (fuera del repo). Verificar si `params.php` está en `.gitignore`.

---

### SEC-002 — Log de cron AFIP committado al repositorio

**Archivo:** `console/log.cronAfip.txt`
**Hallazgo:** Archivo de log presente en el repositorio Git.
**Riesgo:** Los logs pueden contener CUITs, CTGs, tokens de sesión y datos de operaciones reales de clientes. Exposición de datos sensibles ante cualquier persona con acceso al repo.

**Acción:** Agregar `console/log.cronAfip.txt` (y `console/*.txt`) al `.gitignore`. Purgar del historial con `git filter-branch` o `BFG Repo Cleaner`.

---

### SEC-003 — ElephantIO sin control de versión

**Archivo:** `backend/ElephantIO/` (directorio)
**Hallazgo:** Librería PHP vendorizada directamente en el repositorio, sin gestión Composer.
**Riesgo:** Versión desconocida → posibles vulnerabilidades sin parchear. Sin `composer.lock`, no hay forma de auditar la versión exacta.

**Acción:** Migrar a `elephantio/elephant.io` via Composer. Eliminar el directorio local.

---

## 🟠 Altos

### SEC-004 — CORS wildcard `*` activo

**Archivo:** `backend/config/cors.php`
**Hallazgo:**
```php
'origins' => ['*', 'https://dev.muvinapp.com/', ...]
```
El wildcard `'*'` permite que cualquier origen acceda a la API.
**Riesgo:** Permite ataques CSRF desde cualquier dominio. Expone endpoints a peticiones cross-origin no autorizadas.

**Acción:** Eliminar `'*'` del array de origins. Mantener solo los dominios explícitos de Muvinapp.

---

### SEC-005 — Token JWT aceptado como query string

**Archivo:** `backend/behaviours/Apiauth.php`
**Hallazgo:**
```php
if(isset($_GET['access_token'])){
    $accessToken=$_GET['access_token'];
}
```
**Riesgo:** Los tokens en query string se guardan en logs de servidor (Nginx/Apache), historial del navegador y referer headers. Un atacante con acceso a logs puede extraer tokens válidos.

**Acción:** Deprecar el soporte de token por query string. Exigir siempre header HTTP.

---

### SEC-006 — Controlador de prueba JWT posiblemente en producción

**Archivo:** `backend/controllers/TestJwtFirebaseController.php`
**Hallazgo:** Controlador de testeo de JWT presente en el codebase.
**Riesgo:** Endpoints de debugging en producción pueden exponer información interna del sistema de autenticación.

**Acción:** Verificar si este controlador está enrutado en producción. Si sí, eliminar o proteger con IP whitelist.

---

### SEC-007 — Bypass CORS en generación Excel

**Archivos:** `backend/modules/fertilizantes/controllers/`
**Hallazgo:**
```php
header('Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
header('Content-Disposition: attachment; filename="reporte.xlsx"');
```
Los endpoints que emiten Excel con `header()` directo saltean el filtro CORS de Yii2.
**Riesgo:** Cualquier origen puede solicitar estos reportes si conoce la URL y tiene un token válido.

**Acción:** Mantener CORS explicito o usar el Response de Yii2.

---

## 🟡 Medios

### SEC-008 — SwiftMailer deprecated

**Dependencia:** `yiisoft/yii2-swiftmailer ~2.0`
**Riesgo:** SwiftMailer fue archivado en 2021 y no recibe parches de seguridad.
**Acción:** Migrar a Symfony Mailer (`yiisoft/yii2-symfonymailer`).

---

### SEC-009 — phpoffice/phpspreadsheet versión antigua

**Dependencia:** `phpoffice/phpspreadsheet 1.18.0`
**Riesgo:** Versiones antiguas pueden tener vulnerabilidades de parsing de archivos Excel maliciosos.
**Acción:** Actualizar a la última versión estable.

---

### SEC-010 — Sin rate limiting en endpoints de login

**Hallazgo:** No se observa rate limiting en `LoginController`.
**Riesgo:** Ataques de fuerza bruta contra credenciales de usuarios.
**Acción:** Implementar rate limiting (ej: `yii2-ratelimiter`) en endpoints de autenticación.

---

### SEC-011 — Comentarios con código comentado en Apiauth

**Archivo:** `backend/behaviours/Apiauth.php`
**Hallazgo:** Múltiples bloques de código comentado, incluyendo `echo $accessToken; exit;`
**Riesgo:** Bajo en producción, pero indica deuda de limpieza de código. Un `exit` descomentado accidentalmente sería disruptivo.
**Acción:** Limpiar código muerto del archivo.

---

## 🟢 Bajos

### SEC-012 — PHP 7.4 end-of-life

**Hallazgo:** El sistema usa PHP 7.4 (soporte finalizado Nov 2022).
**Riesgo:** Sin parches de seguridad del lenguaje.
**Acción:** Migrar a PHP 8.1+ (ver [[recomendaciones-modernizacion]]).

---

### SEC-013 — Doble función `beforeAction` en ControlDAcciones

**Archivo:** `backend/behaviours/ControlDAcciones.php`
**Hallazgo:** La clase tiene dos métodos `beforeAction()` — el segundo sobreescribe al primero. En PHP esto es un error, pero en producción el segundo método (VerbFilter) es el que prevalece.
**Riesgo:** Comportamiento inesperado si PHP cambia cómo maneja esto.
**Acción:** Refactorizar la clase para tener un solo `beforeAction`.

---

## OWASP Top 10 — Evaluación

| Categoría OWASP | Estado | Nota |
|----------------|--------|------|
| A01: Broken Access Control | 🟡 Parcial | RBAC implementado, pero CORS wildcard |
| A02: Cryptographic Failures | 🟡 Parcial | JWT OK, pero token `123456` en params |
| A03: Injection | 🟢 Bajo riesgo | Yii2 ActiveRecord + prepared statements |
| A04: Insecure Design | 🟡 Medio | Sin rate limiting en login |
| A05: Security Misconfiguration | 🔴 Crítico | CORS `*`, log en repo, credencial hardcoded |
| A06: Vulnerable Components | 🟠 Alto | SwiftMailer deprecated, ElephantIO sin versión |
| A07: Auth Failures | 🟡 Parcial | Token en query string, sin refresh token |
| A08: Data Integrity Failures | 🟢 Bajo | Composer con lock file |
| A09: Security Logging | 🟡 Parcial | Auditoría en BD, pero log en repo |
| A10: SSRF | 🟢 Bajo | cURL a servicios conocidos |
