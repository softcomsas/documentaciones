# Inventario de seguridad

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21
> **Metodología:** Revisión estática del código fuente.

## Resumen ejecutivo

| Severidad | Cantidad | Áreas afectadas |
|-----------|----------|-----------------|
| 🔴 Crítica | 2 | Logging de datos sensibles, pérdida de contexto de errores |
| 🟡 Media | 3 | Autenticación al backend legacy, URL hardcodeada, sin validación de query params |
| 🟢 Baja | 2 | Imagen Docker no-root, variables de entorno validadas |

---

## Hallazgos críticos 🔴

### SEC-001 — Datos sensibles en logs (console.log)

| Atributo | Valor |
|----------|-------|
| **Severidad** | 🔴 Crítica |
| **Archivo** | `src/controller.ts` líneas 33 y 37 |
| **OWASP** | A09:2021 – Security Logging and Monitoring Failures |

**Descripción:**

```typescript
console.log(payload);  // línea 33 — imprime el payload completo del mensaje TCP
console.log(res);      // línea 37 — imprime la respuesta completa del backend
```

El payload puede contener datos de búsqueda (razón social, parámetros de paginación). La respuesta puede contener datos de personas jurídicas (CUIT, razón social). Estos datos aparecen en los logs del contenedor **sin control de nivel, sin sanitización, sin enmascaramiento**, y cualquier sistema que agregue logs (ELK, CloudWatch, Datadog) los almacenará en texto plano.

**Recomendación:** Reemplazar ambos `console.log` por `LOG()` del sistema de logging interno, o eliminarlos si son solo artefactos de debugging.

---

### SEC-002 — Pérdida de contexto de errores

| Atributo | Valor |
|----------|-------|
| **Severidad** | 🔴 Crítica |
| **Archivo** | `src/controller.ts` líneas 41-45 |
| **OWASP** | A09:2021 – Security Logging and Monitoring Failures |

**Descripción:**

```typescript
} catch (error) {
  LOG(`[AppController] Error: ${error}`);
  throw new Error();  // ← error sin mensaje ni stack trace
}
```

El bloque `catch` registra el error con `LOG` pero luego lanza `new Error()` sin mensaje ni causa. El consumidor TCP recibe una excepción sin información sobre qué falló. Esto dificulta el debugging en producción y puede ocultar errores de seguridad reales.

**Recomendación:** Propagar el error original o lanzar con mensaje descriptivo: `throw new Error(\`[AppController] Request failed: ${error.message}\`)`.

---

## Hallazgos de severidad media 🟡

### SEC-003 — Autenticación hacia el backend legacy no verificada

| Atributo | Valor |
|----------|-------|
| **Severidad** | 🟡 Media |
| **Archivo** | `src/service.ts` |
| **OWASP** | A07:2021 – Identification and Authentication Failures |

**Descripción:** No se observa ningún mecanismo de autenticación en las llamadas HTTP de `AppService`. No hay headers `Authorization`, `X-Api-Key` ni `Basic Auth` configurados. Si el backend legacy tiene sus endpoints protegidos, la autenticación puede estar resuelta en otra capa (ej. VPN, IP whitelist) o puede estar ausente.

**Recomendación:** Confirmar el mecanismo de autenticación del backend legacy y documentarlo. Si requiere token, implementarlo como header en `HttpModule.registerAsync`.

---

### SEC-004 — URL del backend legacy hardcodeada en docker-compose

| Atributo | Valor |
|----------|-------|
| **Severidad** | 🟡 Media |
| **Archivo** | `docker/docker-compose.yml` línea 20 |
| **OWASP** | A05:2021 – Security Misconfiguration |

**Descripción:**

```yaml
- LEGACY_MICROSERVICE_BASE_URL=${LEGACY_MICROSERVICE_BASE_URL:-https://dev.muvinapp.com/api/backend/web/}
```

El valor por defecto apunta al ambiente de desarrollo. Si el docker-compose se usa en producción sin sobreescribir esta variable, el tráfico iría al ambiente de desarrollo.

**Recomendación:** Eliminar el valor por defecto de producción en el compose. Exigir que la variable esté explícitamente configurada en el entorno de despliegue.

---

### SEC-005 — Sin validación de query params de entrada

| Atributo | Valor |
|----------|-------|
| **Severidad** | 🟡 Media |
| **Archivo** | `src/api/queries/comprador-by-razon-social.ts`, `src/types/params.ts` |
| **OWASP** | A03:2021 – Injection |

**Descripción:** Los `queryParams` (`razonSocial`, `page`) se pasan directamente al backend legacy sin validación de formato, longitud ni caracteres especiales. Si bien se usa `URLSearchParams` (que escapa los valores), no hay limit de longitud ni validación de tipo más allá del tipado TypeScript en compilación.

**Recomendación:** Agregar un DTO con `class-validator` para los query params de cada endpoint, o al menos validar `page` como entero positivo y `razonSocial` con longitud máxima.

---

## Aspectos con riesgo bajo o correctamente implementados 🟢

### SEC-OK-001 — Contenedor Docker con usuario no-root

**Archivo:** `docker/Dockerfile`

El Dockerfile crea un grupo `nodejs` y usuario `nestjs` no privilegiados y asigna el directorio de trabajo a ese usuario antes de ejecutar la aplicación. Esto reduce el impacto de una eventual explotación del proceso.

### SEC-OK-002 — Variables de entorno validadas al arranque

**Archivo:** `src/config/environments.ts`

La validación con Joi al arranque garantiza que el proceso no se inicia con configuración incompleta o malformada. Comportamiento correcto de **fail-fast**.

---

## Aspectos no evaluados (requieren acceso adicional)

| Aspecto | Razón |
|---------|-------|
| Autenticación TCP entre microservicios | Depende de la configuración de red del ecosistema Muvin, no visible en este repo |
| Secrets en variables de entorno | No hay `.env` en el repo (correcto); se desconoce el mecanismo de gestión de secrets en producción |
| Logs de producción | No se puede auditar el destino final de los logs sin acceso a la infraestructura |
| Vulnerabilidades de dependencias | ⚠️ Pendiente de verificar — ejecutar `npm audit` |
