# Inventario de Seguridad

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21
> **Marco:** OWASP Top 10 + análisis propio

---

## Resumen ejecutivo

| Nivel | Cantidad |
|---|---|
| 🔴 CRÍTICO | 3 |
| 🟡 MEDIO | 4 |
| 🟢 BAJO / INFO | 2 |

---

## 🔴 Riesgos Críticos

### SEC-01 — Clave privada expuesta en payload de Bull

**Archivo:** `src/modules/gmail/service.ts` → método `notification()`
**Descripción:** El job que se encola en Redis incluye el objeto `auth` completo del JWT, que contiene la **clave privada PEM** del service account.

```typescript
// src/modules/gmail/service.ts (payload de queue.add)
{
  id: message.id,
  label: matchingLabel,
  auth: {
    email: this._token.email,
    key: this._token.key,   // ← CLAVE PRIVADA EN TEXTO PLANO EN REDIS
    project: this._token.project,
    domain: this._token.domain,
    scopes: this._token.scopes
  }
}
```

**Impacto:** Cualquier proceso que pueda leer Redis (o un dump de Redis) obtiene la clave privada del service account con DWD sobre todo el dominio de Google Workspace.
**Recomendación:** Eliminar `auth` del payload. El worker debe obtener la credencial directamente desde la base de datos o un secret manager.

---

### SEC-02 — Claves privadas en texto plano en MySQL

**Archivo:** `prisma/schema.prisma` → tabla `gmail_credentials.key`
**Descripción:** Las claves privadas PEM se almacenan en la columna `key` como `TEXT` sin cifrado.
**Impacto:** Un dump de MySQL, un acceso no autorizado a la base de datos, o un log de query contiene la clave privada.
**Recomendación:** Cifrar el campo `key` at-rest (AES-256-GCM) o mover las credenciales a un secret manager (HashiCorp Vault, AWS Secrets Manager, Google Secret Manager).

---

### SEC-03 — Sin autenticación en el transporte TCP

**Archivo:** `src/main.ts`, `src/modules/gmail/controller.ts`
**Descripción:** El microservicio escucha en TCP sin ningún mecanismo de autenticación. Cualquier proceso que alcance el puerto puede enviar mensajes.
**Impacto:** En un entorno con red incorrectamente segmentada, un atacante puede enviar `integrations.email.notification` con cualquier email y desencadenar queries a Gmail API y escrituras en DB.
**Recomendación:** Añadir autenticación a nivel de mensaje (HMAC, shared secret) o restringir el acceso al puerto a nivel de red/firewall estrictamente.

---

## 🟡 Riesgos Medios

### SEC-04 — console.error con variables de entorno en logs

**Archivo:** `src/config/environments.ts`
**Descripción:** Existe un `console.error` con un `// TODO: Remover` que puede imprimir variables de entorno en los logs.
**Impacto:** Las variables de entorno (`DATABASE_URL`, credenciales de Redis) podrían aparecer en logs de producción.
**Recomendación:** Eliminar el `console.error` inmediatamente.

---

### SEC-05 — Watch de Gmail sin renovación — ventana de pérdida

**Descripción:** Los watches de Gmail expiran ~7 días. Sin renovación, el servicio deja de recibir notificaciones silenciosamente.
**Impacto:** Pérdida de mensajes/notificaciones durante el tiempo sin watch activo.
**Recomendación:** Implementar un cron que renueve watches antes de su expiración.

---

### SEC-06 — Credencial de service account única y sin rotación

**Descripción:** Una sola fila en `gmail_credentials`. No hay mecanismo de rotación de credenciales.
**Impacto:** Si la clave se compromete, hay que intervención manual.
**Recomendación:** Diseño multi-credencial con rotación periódica.

---

### SEC-07 — Ausencia total de tests

**Descripción:** No existe ningún test (unit, integration, e2e) en el proyecto.
**Impacto:** Cualquier cambio podría introducir regresiones de seguridad sin detección.
**Recomendación:** Ver [[deuda-tecnica]].

---

## 🟢 Informativos

### SEC-08 — ValidationPipe con whitelist activo

**Archivo:** `src/main.ts`
**Descripción:** Se usa `ValidationPipe({ whitelist: true, forbidNonWhitelisted: true })` — buena práctica.
**Estado:** ✅ Correcto.

### SEC-09 — `Object.freeze` en token de credencial

**Archivo:** `src/modules/gmail/service.ts`
**Descripción:** El token de credencial se define como propiedad no configurable y no writable. Reduce el riesgo de mutación accidental.
**Estado:** ✅ Correcto, aunque no impide lectura.

---

## Ver también

- [[hotspots]]
- [[deuda-tecnica]]
- [[entidad-gmail-credentials]]
- [[modulo-gmail]]
