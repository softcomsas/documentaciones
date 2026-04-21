# Recomendaciones de Modernización

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Prioridad:** Ordenadas por impacto/esfuerzo

---

## Prioridad 1 — Correcciones críticas (hacerlo ahora)

### MOD-01 — Implementar persistencia del resultado

**Problema:** [[deuda-tecnica#DT-01]] — `console.log(data, name)` descarta `ITransferencia`

**Recomendación:** Definir el destino del dato con el equipo de producto y agregar el cliente correspondiente:

```typescript
// Opción A: Publicar en cola downstream (si el API Muvin lo consume)
@InjectQueue('internal') private internalQueue: Queue

await this.internalQueue.add('internal.notification', {
  transferencia: data,
  entityId: job.data.entity.id,
  jobId: job.data.id,
})

// Opción B: Llamada HTTP al API Muvin
await this.muvinApiService.patch(`/transferencias/${job.data.id}`, { data })
```

---

### MOD-02 — Cosechas dinámicas (no hardcodeadas)

**Problema:** [[deuda-tecnica#DT-02]] — falla total en campaña 2829

**Recomendación:**

```typescript
// Opción A: Validación por patrón (sin hardcoding)
function isValidCosecha(value: string): boolean {
  if (!/^\d{4}$/.test(value)) return false;
  const first = parseInt(value.substring(0, 2));
  const second = parseInt(value.substring(2, 4));
  return second === (first + 1) % 100;
}

// Opción B: Variable de entorno
const VALID_COSECHAS = process.env.VALID_COSECHAS?.split(',') ?? [];
```

---

### MOD-03 — Externalizar catálogo de granos

**Problema:** [[deuda-tecnica#DT-03]] — solo TRIGO PAN y SOJA

**Recomendación:**

```typescript
// Cargar desde BD en arranque (en un módulo de configuración)
// O desde variable de entorno como JSON
const GRAINS: { id: number; name: string }[] = JSON.parse(
  process.env.GRAIN_CATALOG ?? '[]'
);
```

---

### MOD-04 — Credenciales fuera del payload del job

**Problema:** [[security-inventory#SEC-01]] — clave privada en Redis

**Recomendación:** Usar GCP Secret Manager o AWS Secrets Manager:

```typescript
// El payload del job solo lleva el ID de la credencial
interface IJobEmailPdf {
  id: number;
  gmail: { messageId: string };
  entity: { id: number; rs: string; cuit: string };
  credentialRef: string;  // ej: 'sm://projects/xxx/secrets/gmail-key-empresa-1'
}

// El worker resuelve la credencial en runtime
const credential = await secretManagerService.get(job.data.credentialRef);
```

---

## Prioridad 2 — Mejoras de calidad (próximo sprint)

### MOD-05 — Redis con autenticación y TLS

**Problema:** [[security-inventory#SEC-02]]

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

### MOD-06 — Validación del payload del job

**Problema:** [[security-inventory#SEC-03]]

```typescript
import Joi from 'joi';

const jobSchema = Joi.object({
  id: Joi.number().required(),
  gmail: Joi.object({ messageId: Joi.string().required() }).required(),
  entity: Joi.object({
    id: Joi.number().required(),
    rs: Joi.string().required(),
    cuit: Joi.string().required(),
  }).required(),
  auth: Joi.object({
    email: Joi.string().email().required(),
    key: Joi.string().required(),
    scopes: Joi.array().items(Joi.string()).required(),
    domain: Joi.string().required(),
    local: Joi.string().required(),
  }).required(),
});
```

---

### MOD-07 — Scopes OAuth hardcodeados en el worker

**Problema:** [[security-inventory#SEC-05]]

```typescript
private _jwt(value: IJobEmailPdf['auth']): Auth.JWT {
  return new google.auth.JWT({
    email: value.email,
    key: value.key,
    scopes: ['https://www.googleapis.com/auth/gmail.readonly'],
    subject: `${value.local}@${value.domain}`,
  });
}
```

---

## Prioridad 3 — Modernización de stack (roadmap)

### MOD-08 — Migrar de Bull a BullMQ

**Bull** (versión 4) está en mantenimiento. **BullMQ** es el sucesor con soporte activo:

```bash
npm install bullmq @nestjs/bullmq
npm uninstall bull @nestjs/bull
```

Ventajas: mejores tipos TypeScript, soporte nativo de Redis Clusters, mejor manejo de concurrencia.

---

### MOD-09 — Reemplazar `pdf-parse` por `pdf.js`

`pdf-parse` tiene bajo mantenimiento. Alternativas:

| Librería | Ventajas | Consideraciones |
|---------|----------|-----------------|
| `pdfjs-dist` (Mozilla) | Mantenimiento activo, soporta PDFs complejos | API más verbosa |
| `pdf-lib` | Creación y modificación de PDFs | No extrae texto |
| `unpdf` | Wrapper moderno sobre pdfjs | Nuevo, menos adoptado |

---

### MOD-10 — Agregar tests unitarios para `rt.ts`

El hotspot principal [[hotspots#Hotspot-1]] no tiene tests. Con ~430 líneas y ~25 ramas condicionales, un refactor sin tests es riesgoso.

**Fixtures recomendadas:**
1. PDF de TRIGO PAN válido → debe retornar `{ success: true, data: ITransferencia }`
2. PDF de maíz → debe retornar `{ success: false, errors: ['grano no reconocido'] }`
3. PDF con CUIT faltante → `{ success: false, errors: ['CUITs insuficientes'] }`
4. PDF con cosecha fuera de rango → `{ success: false, errors: [...] }`
5. PDF corruptoilegible → debe lanzar excepción (capturada en processor)

---

### MOD-11 — Docker Compose de desarrollo con Redis

Reemplazar el MySQL actual con un compose funcional:

```yaml
# docker-compose.yml correcto para este worker
services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --requirepass ${REDIS_PASSWORD:-dev_password}

  worker:
    build:
      context: .
      dockerfile: docker/Dockerfile
    environment:
      HOST: redis
      PORT: 6379
      REDIS_PASSWORD: dev_password
    depends_on:
      - redis
```
