# Inventario de Seguridad

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Metodología:** OWASP Top 10 + revisión manual del código fuente

---

## Resumen ejecutivo

| Criticidad | Cantidad | Issues |
|:----------:|:--------:|--------|
| 🔴 Alta | 2 | Clave privada en Redis, credenciales en payload |
| 🟡 Media | 3 | Sin auth Redis, dep innecesaria, sin validación payload |
| 🟢 Baja | 1 | Scope excesivo posible |

---

## Hallazgos

### 🔴 SEC-01 — Clave privada RSA viaja en Redis sin cifrar

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🔴 Alta |
| **Categoría OWASP** | A02: Cryptographic Failures |
| **Archivo** | `src/common/interfaces/jobs/email/pdf.ts` |
| **Campo** | `IJobEmailPdf.auth.key` |

**Descripción:** El campo `auth.key` del payload del job contiene la clave privada PEM de la Service Account de Google. Esta clave se serializa a JSON y se almacena en Redis en texto plano. Cualquier proceso con acceso de lectura a Redis (otra aplicación, un script de monitoreo, un dump de backup) puede obtener esta clave privada.

**Impacto:** Acceso completo a la cuenta de servicio de Google → lectura de todos los emails del dominio corporativo, impersonación de cualquier usuario en el dominio (si DWD está habilitado ampliamente).

**Mitigación recomendada:**
1. Almacenar las credenciales en un secret manager (AWS Secrets Manager, GCP Secret Manager, HashiCorp Vault)
2. El payload del job debe contener solo un identificador/referencia de la credencial, no la credencial misma
3. El worker recupera las credenciales en el momento del procesamiento usando el ID de referencia

---

### 🔴 SEC-02 — Sin autenticación ni cifrado en Redis

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🔴 Alta |
| **Categoría OWASP** | A02: Cryptographic Failures + A07: Identification and Authentication Failures |
| **Archivo** | `src/config/environments.ts`, `src/module.ts` |

**Descripción:** La conexión Bull a Redis se configura con solo `host` y `port`. No hay `password`, no hay configuración de TLS/SSL. Combinado con SEC-01, esto significa que las claves privadas circulan en plaintext por una conexión de red no autenticada.

**Impacto:** Un atacante en la red puede leer el contenido de la cola Redis con un sniffer o conectándose directamente al puerto Redis.

**Mitigación recomendada:**
```typescript
BullModule.forRoot({
  redis: {
    host: ENVIRONMENTS.HOST,
    port: ENVIRONMENTS.PORT,
    password: process.env.REDIS_PASSWORD,
    tls: process.env.REDIS_TLS === 'true' ? {} : undefined,
  }
})
```

---

### 🟡 SEC-03 — Sin validación del payload del job en runtime

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟡 Media |
| **Categoría OWASP** | A03: Injection |
| **Archivo** | `src/modules/email/processor.ts` |

**Descripción:** El worker trata `job.data` como `IJobEmailPdf` sin ninguna validación en runtime. Si el API Muvin publica un job con una estructura incorrecta o malformada, el error ocurrirá en profundidad (dentro de la llamada a Gmail API o el parsing de PDF) con mensajes confusos.

**Impacto:** Errores difíciles de depurar; en el peor caso, acceso a propiedades undefined puede resultar en comportamiento inesperado.

**Mitigación recomendada:** Agregar validación con `joi` o `class-validator` al inicio de `handleMail()`.

---

### 🟡 SEC-04 — Dependencia `@nestjs/platform-express` innecesaria

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟡 Media |
| **Categoría OWASP** | A06: Vulnerable and Outdated Components |
| **Archivo** | `package.json` |

**Descripción:** `@nestjs/platform-express` está en `dependencies` aunque el worker no expone ningún servidor HTTP. Express (y todas sus dependencias transitivas) carga en producción con su superficie de ataque completa, sin ningún beneficio.

**Mitigación recomendada:** Remover de `dependencies`. Si NestFactory lo requiere internamente, puede usar `@nestjs/core` directamente o instalar `@nestjs/platform-fastify` solo si algún día se necesita HTTP.

---

### 🟡 SEC-05 — Scopes OAuth no validados en el worker

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟡 Media |
| **Categoría OWASP** | A01: Broken Access Control |
| **Archivo** | `src/modules/email/processor.ts` (`_jwt()`) |

**Descripción:** Los scopes OAuth se pasan directamente del payload del job al JWT. No hay validación de que sean los scopes esperados (`gmail.readonly`). Un publicador malicioso o comprometido podría enviar scopes más amplios como `gmail` (acceso completo incluyendo envío de emails).

**Mitigación recomendada:** El worker debe usar scopes hardcodeados e ignorar (o rechazar) los del payload:
```typescript
private _jwt(value: IJobEmailPdf['auth']): Auth.JWT {
  const REQUIRED_SCOPES = ['https://www.googleapis.com/auth/gmail.readonly'];
  return new google.auth.JWT({
    email: value.email,
    key: value.key,
    scopes: REQUIRED_SCOPES,  // ignorar value.scopes
    subject: `${value.local}@${value.domain}`,
  });
}
```

---

### 🟢 SEC-06 — Información en logs (`console.error`)

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟢 Baja |
| **Categoría OWASP** | A09: Security Logging and Monitoring Failures |
| **Archivo** | `src/modules/email/processor.ts` |

**Descripción:** Los `console.error` pueden incluir el objeto `err` completo, que en el caso de errores de la Gmail API puede contener headers HTTP con tokens de autorización. El logging sin sanitización puede exponer tokens Bearer.

**Mitigación recomendada:** Usar un logger estructurado que sanitice objetos de error antes de serializar.
