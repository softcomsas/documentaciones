# Flujo: Validación y Autenticación

**Última actualización:** 2026-04-27

## Diagrama de Secuencia

```
Cliente              ms-commercial         MySQL         ms-auth
  │                      │                  │             │
  │ create-key           │                  │             │
  ├─────────────────────>│                  │             │
  │                      │ Validar company  │             │
  │                      ├─────────────────>│             │
  │                      │ OK               │             │
  │                      │<─────────────────┤             │
  │                      │                  │             │
  │                      │ Generar key+secret
  │                      │ INSERT en keys   │             │
  │                      ├─────────────────>│             │
  │                      │ OK               │             │
  │                      │<─────────────────┤             │
  │ {key, secret}        │                  │             │
  │<─────────────────────┤                  │             │
  │                      │                  │             │
  │ ─ ALMACENA SECRET ─  │                  │             │
  │                      │                  │             │
  │ (después, en otra llamada)
  │                      │                  │             │
  │ validate-key         │                  │             │
  ├─────────────────────>│                  │             │
  │ {key, signature}     │                  │             │
  │                      │ Buscar key       │             │
  │                      ├─────────────────>│             │
  │                      │ {key, secret}    │             │
  │                      │<─────────────────┤             │
  │                      │                  │             │
  │                      │ Verificar firma  │             │
  │                      │ HMAC-SHA256(...) │             │
  │                      │                  │             │
  │ {valid: true/false}  │                  │             │
  │<─────────────────────┤                  │             │
  │                      │                  │             │
```

---

## Pasos Detallados

### Fase 1: Crear API Key

#### 1.1 Recibir Request

```typescript
@MessagePattern('create-key')
async createKey(@Payload() payload: { company_id: number })
```

#### 1.2 Validar Company

```typescript
const company = await this.entitiesRepository.findById(payload.company_id);
if (!company) throw NotFoundException('Company not found');
```

Query a `entities` table.

#### 1.3 Generar Key + Secret

```typescript
const key = crypto.randomBytes(32).toString('hex');        // 64 chars
const secret = crypto.randomBytes(64).toString('hex');     // 128 chars
```

Generación criptográfica aleatoria.

#### 1.4 Guardar en BD

```typescript
const apiKey = await this.keysRepository.create({
  key,
  secret,
  company_id: payload.company_id,
  active: true,
});
```

INSERT en tabla `keys`.

#### 1.5 Retornar (Una Sola Vez)

```typescript
return {
  success: true,
  data: {
    key,
    secret,  // ← SOLO AQUÍ se retorna
    active: true,
    company_id: payload.company_id,
  },
  error: null
};
```

**NOTA:** El cliente debe guardar el secret. No se puede recuperar después.

---

### Fase 2: Validar Key (Autenticación)

#### 2.1 Recibir Request

```typescript
@MessagePattern('validate-key')
async validateKey(@Payload() payload: { key: string, signature: string })
```

Payload incluye:
- `key`: API key (hexadecimal, 64 chars)
- `signature`: HMAC SHA256(key + data, secret)

#### 2.2 Buscar Key en BD

```typescript
const apiKey = await this.keysRepository.findByKey(payload.key);
if (!apiKey) {
  logger.warn(`Invalid key attempt: ${payload.key}`);
  return { success: true, data: { valid: false }, error: null };
}
```

Query a tabla `keys`.

#### 2.3 Verificar Estado

```typescript
if (!apiKey.active) {
  return { success: true, data: { valid: false }, error: null };
}
```

Si está desactivada, rechazar.

#### 2.4 Verificar Firma

```typescript
const expectedSignature = crypto
  .createHmac('sha256', apiKey.secret)
  .update(payload.key + payload.data)
  .digest('hex');

if (payload.signature !== expectedSignature) {
  logger.warn(`Invalid signature for key: ${payload.key}`);
  return { success: true, data: { valid: false }, error: null };
}
```

Regenerar firma esperada y comparar.

#### 2.5 Retornar Resultado

```typescript
return {
  success: true,
  data: {
    valid: true,
    company_id: apiKey.company_id,
    active: true,
  },
  error: null
};
```

---

## Cómo Generar Firma en Cliente

```javascript
const crypto = require('crypto');

const key = 'a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3';
const secret = 'x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3...';
const data = 'my-request-data';

const signature = crypto
  .createHmac('sha256', secret)
  .update(key + data)
  .digest('hex');

// Usar en RPC
client.send('validate-key', {
  key,
  signature
});
```

---

## Seguridad

> [!danger]
> **NUNCA enviar el secret en la red.** Solo usarlo para generar firma localmente.

> [!warning]
> **Rate limiting:** En producción, limitar intentos de validación para prevenir fuerza bruta.

---

## Documentos Relacionados

- [[../02-funcionalidades/validation-create-key|Create API Key]] - API detail
- [[../02-funcionalidades/validation-validate-key|Validate Key]] - API detail
- [[../02-funcionalidades/validation-generate-signature|Generate Signature]] - Herramienta
- [[_indice-flujos|Índice de Flujos]] - Otros flujos

