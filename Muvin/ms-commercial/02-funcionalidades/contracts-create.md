# Funcionalidad: Crear Contrato

**Pattern:** `commercial.contracts.create`  
**Módulo:** Contracts  
**Tipo:** WRITE  
**Última actualización:** 2026-04-27

## Descripción

Crea un nuevo contrato comercial con todas las validaciones de negocio necesarias. El balance inicial se establece igual al límite de cupo.

---

## Entrada (Payload)

```typescript
{
  reference: number;          // Número de referencia del contrato (único por empresa)
  company_cuit: string;       // CUIT de empresa (11 dígitos)
  client_cuit: string;        // CUIT del cliente
  broker_cuit?: string;       // CUIT del corredor (opcional)
  destination_cuit: string;   // CUIT del destino
  product_code: number;       // Código del producto
  limit: number;              // Cupo inicial (positivo)
  volume?: number;            // Volumen (opcional)
  price: Decimal;             // Precio por unidad
  start: Date;                // Fecha de inicio de vigencia
  end: Date;                  // Fecha de fin de vigencia
  priority?: string;          // Prioridad (HIGHEST, HIGH, MEDIUM, LOW, LOWEST)
  api_key?: string;           // API key para validación (opcional)
  signature?: string;         // Firma HMAC (si se usa API key)
}
```

### Campos Requeridos vs Opcionales

| Campo | Requerido | Validación |
|-------|-----------|-----------|
| reference | ✅ | > 0, número |
| company_cuit | ✅ | 11 dígitos, debe existir |
| client_cuit | ✅ | 11 dígitos, debe existir |
| broker_cuit | ❌ | 11 dígitos si provided |
| destination_cuit | ✅ | 11 dígitos, debe existir |
| product_code | ✅ | número, debe existir |
| limit | ✅ | > 0 |
| volume | ❌ | >= 0 si provided |
| price | ✅ | >= 0 |
| start | ✅ | datetime, < end |
| end | ✅ | datetime, > start |
| priority | ❌ | enum: HIGHEST\|HIGH\|MEDIUM\|LOW\|LOWEST, defecto: MEDIUM |

---

## Salida (Response)

```typescript
// Éxito (HTTP 200)
{
  success: true,
  data: {
    id: number;             // ID del nuevo contrato en BD
    reference: number;      // Reference (eco del input)
    limit: number;          // Cupo (eco del input)
    balance: number;        // = limit (inicial)
    status: 'OPEN';         // Siempre OPEN al crear
  },
  error: null
}

// Error (HTTP 400-500)
{
  success: false,
  data: null,
  error: "Descripción del error"
}
```

---

## Validaciones Aplicadas

### Datos de Entrada
- ✅ Reference es número positivo
- ✅ CUITs tienen 11 dígitos (formato: XX-XXXXXXXX-X)
- ✅ Limit es positivo
- ✅ Start < End
- ✅ Price >= 0
- ✅ Volume >= 0 (si provided)

### Existencia de Entidades
- ✅ Company CUIT existe en tabla `entities`
- ✅ Client CUIT existe en tabla `entities`
- ✅ Broker CUIT existe (si provided)
- ✅ Destination CUIT existe
- ✅ Product code existe en tabla `products`

### Unicidad
- ✅ (reference, company_id) es única → no puede haber dos contratos con mismo reference en una empresa

### Lógica de Negocio
- ✅ Balance inicial = Limit
- ✅ Status inicial = 'OPEN'
- ✅ Priority por defecto = 'MEDIUM'

---

## Ejemplos de Uso

### Caso 1: Crear Contrato Básico

```bash
curl -X POST http://localhost:4005 \
  -H "Content-Type: application/json" \
  -d '{
    "pattern": "commercial.contracts.create",
    "payload": {
      "reference": 1001,
      "company_cuit": "20-12345678-9",
      "client_cuit": "30-98765432-1",
      "destination_cuit": "27-11111111-1",
      "product_code": 1,
      "limit": 10000,
      "price": 100.50,
      "start": "2026-01-01T00:00:00Z",
      "end": "2026-12-31T23:59:59Z"
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "reference": 1001,
    "limit": 10000,
    "balance": 10000,
    "status": "OPEN"
  },
  "error": null
}
```

---

### Caso 2: Crear Contrato con Broker

```typescript
const payload = {
  reference: 1002,
  company_cuit: "20-12345678-9",
  client_cuit: "30-98765432-1",
  broker_cuit: "27-44444444-4",        // ← Broker incluido
  destination_cuit: "27-11111111-1",
  product_code: 2,
  limit: 5000,
  volume: 100,                         // ← Volume incluido
  price: 50.00,
  start: "2026-02-01T00:00:00Z",
  end: "2026-11-30T23:59:59Z",
  priority: "HIGH"                     // ← Priority override
};

// Enviar RPC message
const response = await client.send('commercial.contracts.create', payload);
```

---

### Caso 3: Crear con Validación de API Key

```typescript
// Primero validar la API key
const validateResponse = await client.send('validate-key', {
  key: 'abc123...',
  signature: 'xyz789...'
});

if (validateResponse.data.valid) {
  // Luego crear contrato
  const createResponse = await client.send('commercial.contracts.create', {
    reference: 1003,
    // ... resto de datos
  });
}
```

---

## Errores Comunes

### Error: "Company not found"
**Causa:** El CUIT de empresa no existe en tabla `entities`  
**Solución:** Verificar que el CUIT es correcto, crear la entidad si falta

```json
{
  "success": false,
  "data": null,
  "error": "Company CUIT 20-12345678-9 not found"
}
```

---

### Error: "Contract already exists"
**Causa:** Ya existe un contrato con esa reference en esa empresa  
**Solución:** Usar diferente reference, o actualizar el contrato existente

```json
{
  "success": false,
  "data": null,
  "error": "Contract with reference 1001 already exists for company 5"
}
```

---

### Error: "Invalid CUIT format"
**Causa:** El CUIT no tiene 11 dígitos  
**Solución:** Validar formato del CUIT (XX-XXXXXXXX-X)

```json
{
  "success": false,
  "data": null,
  "error": "CUIT 20-123456 is invalid (must be 11 digits)"
}
```

---

### Error: "Product not found"
**Causa:** El product_code no existe en tabla `products`  
**Solución:** Verificar código del producto, crearlo si falta

```json
{
  "success": false,
  "data": null,
  "error": "Product code 999 not found"
}
```

---

### Error: "Start date must be before end date"
**Causa:** start >= end  
**Solución:** Ajustar fechas

```json
{
  "success": false,
  "data": null,
  "error": "Start date 2026-12-31 must be before end date 2026-01-01"
}
```

---

## Estados Posteriores

Después de crear un contrato, es posible:

1. **Buscar el contrato** → [[contracts-search-one|search.one]]
2. **Actualizar cupo** → [[contracts-change-limit|change.limit]]
3. **Actualizar balance** → [[contracts-change-balance|change.balance]]
4. **Listar contratos** → [[contracts-search-list|search.list]]

---

## Validaciones de Seguridad

> [!warning]
> Este endpoint es **CRÍTICO** porque modifica la BD. En producción, puede requerir:
> - Validación de API key
> - Firma HMAC
> - Rate limiting
> - Logging de auditoría

---

## Nota Técnica

### Resolución CUIT → ID

El servicio resuelve CUITs a IDs internos automáticamente:

```typescript
const companyId = await entitiesRepository.findByCuit(payload.company_cuit);
if (!companyId) throw new NotFoundException('...');

// Luego usa el ID internamente
await contractsRepository.create({
  company: companyId,
  // ... resto
});
```

---

## Performance

| Métrica | Valor |
|---------|-------|
| Queries a BD | 4-5 (validar entities, product, crear contract) |
| Complejidad | O(1) si hay índices |
| Tiempo esperado | 10-50ms |
| Throughput | 20-100 creaciones/segundo |

---

## Documentos Relacionados

- [[_indice-funcionalidades|Índice de Funcionalidades]] - Todas las acciones
- [[../01-modulos/modulo-contracts|Módulo Contracts]] - Implementación
- [[../04-modelo-de-datos/entidad-contracts|Entidad Contracts]] - Schema
- [[../06-flujos-transversales/flujo-creacion-contrato|Flujo de Creación]] - End-to-end
- [[validation-validate-key|Validar API Key]] - Para seguridad

