# Funcionalidad: Crear API Key

**Pattern:** `create-key`  
**Módulo:** Validation  
**Tipo:** WRITE  
**Última actualización:** 2026-04-27

## Descripción

Genera una nueva API key y secret para una compañía. El secret se retorna UNA SOLA VEZ, debe ser guardado inmediatamente por el cliente.

---

## Entrada

```typescript
{
  company_id: number;     // ID de la compañía
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    key: string;          // 64 caracteres hexadecimales (público)
    secret: string;       // 128 caracteres hexadecimales (PRIVADO)
    active: boolean;      // true
    company_id: number;
  },
  error: null
}
```

---

## Validaciones

- ✅ Company existe
- ✅ Solo una key por company (UNIQUE)

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "create-key",
    "payload": {
      "company_id": 5
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "key": "a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3",
    "secret": "x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3n5o7p9q2r4s6t8u1v3w5x7y9z",
    "active": true,
    "company_id": 5
  },
  "error": null
}
```

---

## ⚠️ IMPORTANTE

> [!danger]
> **EL SECRET NO SE PUEDE RECUPERAR**
> - Después de crear la key, el secret se retorna UNA VEZ
> - No hay endpoint para recuperar un secret perdido
> - Si se pierde, hay que crear una nueva key
> - Guardar en lugar seguro inmediatamente

---

## Flujo Recomendado

```
1. Llamar create-key
2. Recibir key + secret
3. GUARDAR en lugar seguro (ej: file encriptado, vault)
4. Usar key + secret para generar firmas en futuras llamadas
5. Si se pierde, crear nueva key
```

---

## Documentos Relacionados

- [[validation-validate-key|Validar API Key]] - Usar la key para autenticarse
- [[validation-generate-signature|Generar Firma]] - Generar HMAC con key+secret
- [[_indice-funcionalidades|Índice]] - Todas las acciones

