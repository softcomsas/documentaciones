# Funcionalidad: Generar Firma

**Pattern:** `generate-signature`  
**Módulo:** Validation  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Genera una firma HMAC SHA256. Útil para clientes que necesitan generar firmas de manera centralizada.

---

## Entrada

```typescript
{
  key: string;            // API key
  secret: string;         // API secret
  data: string;           // Datos a firmar
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    signature: string;    // HMAC SHA256 en hexadecimal
  },
  error: null
}
```

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "generate-signature",
    "payload": {
      "key": "a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3",
      "secret": "x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3...",
      "data": "my-request-data"
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "signature": "5f7c3a2e8b4d6c1f9a0e5b3d7c2f8a1e7b4c9d2e5f8a1b4c7d0e3f6a9b2c5"
  },
  "error": null
}
```

---

## Uso

Después de obtener la firma, usarla en [[validation-validate-key|validate-key]]:

```typescript
// 1. Generar firma
const sigResponse = await client.send('generate-signature', {
  key,
  secret,
  data: 'my-data'
});
const signature = sigResponse.data.signature;

// 2. Usar en validación
const validResponse = await client.send('validate-key', {
  key,
  signature
});
```

---

## ℹ️ Alternativa

> [!info]
> Normalmente, el cliente genera la firma localmente en su código (no llamando este endpoint).
> Este endpoint es útil si:
> - No puedes generar HMAC SHA256 localmente
> - Quieres centralizar la generación de firmas

---

## Documentos Relacionados

- [[validation-validate-key|Validar API Key]] - Usar la firma
- [[_indice-funcionalidades|Índice]] - Todas las acciones

