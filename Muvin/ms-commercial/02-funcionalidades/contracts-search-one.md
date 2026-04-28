# Funcionalidad: Buscar Contrato (Uno)

**Pattern:** `commercial.contracts.search.one`  
**Módulo:** Contracts  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Busca un contrato específico usando la combinación **reference + company_cuit**. Retorna los datos básicos del contrato (no requiere toda la información).

---

## Entrada

```typescript
{
  reference: number;      // Número de referencia del contrato
  company_cuit: string;   // CUIT de empresa (11 dígitos)
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    id: number;           // ID interno
    reference: number;    // Eco del input
    limit: number;        // Cupo total
    balance: number;      // Saldo disponible
    status: string;       // OPEN, CLOSED, EXPIRED, VOIDED
  },
  error: null
}
```

---

## Validaciones

- ✅ Reference es número
- ✅ Company CUIT existe
- ✅ Contrato existe con esa combinación

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -H "Content-Type: application/json" \
  -d '{
    "pattern": "commercial.contracts.search.one",
    "payload": {
      "reference": 1001,
      "company_cuit": "20-12345678-9"
    }
  }'
```

**Respuesta exitosa:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "reference": 1001,
    "limit": 10000,
    "balance": 7500,
    "status": "OPEN"
  },
  "error": null
}
```

**Respuesta con error (no encontrado):**
```json
{
  "success": false,
  "data": null,
  "error": "Contract not found for reference 1001 in company 20-12345678-9"
}
```

---

## Errores Comunes

| Error | Causa | Solución |
|-------|-------|----------|
| Company not found | CUIT no existe | Verificar CUIT |
| Contract not found | No existe contrato con esa ref | Crear uno nuevo |

---

## Documentos Relacionados

- [[_indice-funcionalidades|Índice]] - Todas las acciones
- [[contracts-search-list|Buscar Lista]] - Búsqueda paginada
- [[contracts-search-reference|Buscar por Reference]] - Datos completos
- [[contracts-create|Crear Contrato]] - Para crear uno nuevo

