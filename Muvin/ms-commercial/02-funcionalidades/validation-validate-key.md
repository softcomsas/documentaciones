# Funcionalidad: Validar API Key

**Pattern:** `validate-key`  
**Módulo:** Validation  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Verifica que una API key es válida y que la firma HMAC es correcta. Retorna si el cliente está autenticado.

---

## Entrada

```typescript
{
  key: string;            // API key (64 chars)
  signature: string;      // HMAC SHA256 de (key + data, secret)
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    valid: boolean;
    company_id?: number;  // Si válido
    active?: boolean;     // Si válido
  },
  error: null
}
```

---

## Validaciones

- ✅ Key existe en BD
- ✅ Key está activa (active = true)
- ✅ Firma HMAC es correcta
- ✅ Hash: HMAC-SHA256(key + data, secret) en hex

---

## Cómo Generar la Firma

```typescript
const crypto = require('crypto');

// Datos
const key = 'a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3';
const secret = 'x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3...';
const data = 'some-request-data';

// Generar firma
const signature = crypto
  .createHmac('sha256', secret)
  .update(key + data)
  .digest('hex');

// Usar en validación
const response = await client.send('validate-key', {
  key,
  signature
});
```

---

## Ejemplo

```bash
# 1. Generar firma (en cliente)
key = "a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3"
secret = "x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3..."
signature = HMAC-SHA256("a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3" + data, secret)

# 2. Enviar a servidor
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "validate-key",
    "payload": {
      "key": "a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3",
      "signature": "5f7c3a2e8b4d6c1f9a0e5b3d7c2f8a1e..."
    }
  }'
```

**Respuesta (Válido):**
```json
{
  "success": true,
  "data": {
    "valid": true,
    "company_id": 5,
    "active": true
  },
  "error": null
}
```

**Respuesta (Inválido):**
```json
{
  "success": true,
  "data": {
    "valid": false
  },
  "error": null
}
```

---

## Errores Comunes

| Problema | Causa | Solución |
|----------|-------|----------|
| valid=false | Key no existe | Verificar que el key es correcto |
| valid=false | Key no está activa | Generar nueva key |
| valid=false | Firma incorrecta | Regenerar firma con secret correcto |
| valid=false | Secret incorrecto | Usar el secret original |

---

## Flujo de Uso Típico

```
1. Cliente tiene key + secret (guardados)
2. Cliente quiere hacer una solicitud
3. Genera firma: HMAC-SHA256(key + data, secret)
4. Envía: validate-key con key + signature
5. Servidor verifica
6. Si válido → cliente puede usar otros endpoints
```

---

## Security Notes

> [!warning]
> **NUNCA enviar el secret** en la red. Solo usar para generar firma localmente.

> [!info]
> **Hash en hex**: La firma se envía como hexadecimal (64 caracteres).

> [!danger]
> **Rate limiting**: En producción, considerar limitar intentos fallidos para prevenir fuerza bruta.

---

## Documentos Relacionados

- [[validation-create-key|Crear API Key]] - Para obtener key + secret
- [[validation-generate-signature|Generar Firma]] - Para generar HMAC
- [[../01-modulos/modulo-validation|Módulo Validation]] - Implementación
- [[_indice-funcionalidades|Índice]] - Todas las acciones

