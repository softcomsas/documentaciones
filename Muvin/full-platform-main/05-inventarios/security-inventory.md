# Inventario de Seguridad

> **Última revisión:** 2026-04-29
> **Metodología:** Análisis estático de código fuente disponible

---

## Autenticación y Sesión

| Hallazgo | Módulo | Severidad | Descripción |
|---|---|---|---|
| JWT validado en middleware NestJS | descargas-app backend | 🟢 | `TokenMiddleware` en `utils/token.middleware.ts` aplica validación de JWT a todas las rutas (`forRoutes('*')`) |
| Backend auth-app es stub vacío | auth-app backend | 🔴 | `auth-app/backend/api/README.md` no tiene implementación. No está claro dónde ocurre la autenticación backend real. |
| Autenticación Yii2 no alineada con JWT | logistica, oferta, documentacion | 🟡 | Los backends Yii2 tienen su propio mecanismo de autenticación (ActiveRecord + sesión o token propio). Puede no estar integrado con el JWT del monorepo. |
| `jsonwebtoken` usado directamente sin `@nestjs/jwt` | descargas-app backend | 🟡 | Se usa `jsonwebtoken` v9 en lugar del módulo oficial `@nestjs/jwt`. Funciona pero no es el patrón idiomático de NestJS, puede llevar a inconsistencias. |

---

## Control de Acceso (Autorización)

| Hallazgo | Módulo | Severidad | Descripción |
|---|---|---|---|
| Guards Angular en MFEs | main, descargas, logistica, auth | 🟢 | Cada MFE tiene su carpeta `guards/` para proteger rutas de frontend |
| `ConsultarRolesController` en descargas | descargas-app backend | 🟡 | Existe un controlador dedicado `consultar-roles.controller.ts`. No está claro si hay validación de roles a nivel de endpoint (Role Guards en NestJS). ⚠️ Pendiente de verificar |
| Autorización en Yii2 | logistica, oferta, documentacion | 🟡 | La autorización PHP (RBAC de Yii2 o propio) no está documentada. Riesgo de inconsistencia con la plataforma Angular. |

---

## Endpoints sin Autenticación

| Hallazgo | Endpoint | Módulo | Severidad |
|---|---|---|---|
| CronController expuesto HTTP | ⚠️ Ruta pendiente de verificar | descargas-app backend | 🔴 | El `CronController` tiene rutas HTTP que potencialmente permiten disparar cron jobs manualmente. Si no están protegidas con auth, cualquiera podría triggerearlos. |
| SiteController en Yii2 (login/logout) | `/site/login`, `/site/logout` | logistica, oferta, documentacion | 🟡 | Endpoints PHP sin verificar si están debidamente protegidos en entorno de producción |

---

## Manejo de Contraseñas y Credenciales

| Hallazgo | Archivo | Módulo | Severidad |
|---|---|---|---|
| `.env-template` en descargas-app | `descargas-app/backend/.env-template` | descargas-app | 🟢 | Existe template de env, lo que sugiere que las credenciales se manejan por variables de entorno |
| `local.env` en backends Yii2 | `logistica-app/backend/api/source/local.env`, `oferta-app/...`, `documentacion-app/...` | logistica, oferta, documentacion | 🔴 | Archivos `local.env` en los backends Yii2. **Riesgo de credenciales commiteadas** si no están en `.gitignore`. Verificar urgente. |
| Vagrant/VagrantFile en backends Yii2 | `logistica-app/backend/api/source/Vagrantfile` | logistica, oferta, documentacion | 🟡 | Archivos de Vagrant pueden contener configuraciones de acceso hardcodeadas |

---

## Validación de Entrada

| Hallazgo | Archivo | Módulo | Severidad |
|---|---|---|---|
| `NoHtmlPipe` en descargas controller | `descargas.controller.ts` | descargas-app | 🟢 | Valida que los inputs no contengan etiquetas HTML (previene XSS básico). Aunque debería estar en un archivo de pipes separado. |
| `ValidationExceptionFilter` aplicado | `descargas.controller.ts` | descargas-app | 🟢 | Filtro de excepciones de validación activo en endpoints clave |
| `class-validator` en DTOs | descargas-app backend | 🟢 | Uso de `class-validator` para validación de DTOs en NestJS |
| Validación en Yii2 | logistica, oferta, documentacion | 🟡 | ⚠️ Pendiente de verificar validaciones en modelos ActiveRecord de Yii2. El framework valida por reglas en `rules()` pero necesita confirmación |

---

## Inyección SQL / NoSQL

| Hallazgo | Módulo | Severidad |
|---|---|---|
| Sequelize ORM en NestJS | descargas, pedidos, superapp | 🟢 | Uso de ORM parametriza automáticamente las queries; bajo riesgo si no se usan raw queries sin sanitizar |
| ActiveRecord en Yii2 | logistica, oferta, documentacion | 🟡 | Yii2 ActiveRecord protege contra SQL injection si se usan bindings correctamente. ⚠️ Pendiente de revisar uso de raw SQL en modelos PHP |

---

## Logs y Datos Sensibles

| Hallazgo | Archivo | Módulo | Severidad |
|---|---|---|---|
| `LoggingInterceptor` registra requests | `descargas-app/backend/src/utils/logger.interceptor.ts` | descargas-app | 🟡 | Interceptor de logging activo en NestJS. ⚠️ Pendiente de verificar si logea payloads completos (puede incluir datos sensibles como tokens o datos personales) |
| `console.error` en controllers | `descargas.controller.ts` | descargas-app | 🟡 | Múltiples `console.error(error.status)` en los catch. En producción esto puede loggear stack traces sensibles a stdout |
| `LogsService` en descargas | `descargas-app/backend/src/services/logs.service.ts` | descargas-app | 🟢 | Servicio de logs dedicado. ⚠️ Pendiente de verificar si escribe a DB o solo a stdout |

---

## Resumen por Severidad

| Severidad | Cantidad | Descripción |
|---|---|---|
| 🔴 Crítico | 3 | auth-app sin backend, local.env potencialmente commiteado, CronController posiblemente expuesto |
| 🟡 Atención | 7 | jsonwebtoken sin módulo oficial, autenticación Yii2 no alineada, roles sin verificar, Vagrant con config, validaciones Yii2 sin verificar, logger con datos posiblemente sensibles, SiteController PHP |
| 🟢 OK | 6 | JWT middleware, guards Angular, NoHtmlPipe, ValidationExceptionFilter, class-validator, Sequelize ORM |
