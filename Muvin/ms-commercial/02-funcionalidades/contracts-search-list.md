# Funcionalidad: Buscar Contratos (Lista Paginada)

**Pattern:** `commercial.contracts.search.list`  
**Módulo:** Contracts  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Busca contratos con paginación y filtros opcionales. Retorna lista ordenada por fecha de vencimiento, prioridad y reference.

---

## Entrada

```typescript
{
  page: number;           // Página (1-indexed)
  limit: number;          // Resultados por página
  status?: string;        // Filtro opcional (OPEN, CLOSED, etc)
  company_id?: number;    // Filtro opcional por empresa
  sort?: string;          // Campo de ordenamiento (end, priority, reference)
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    contracts: [
      {
        id: number;
        reference: number;
        limit: number;
        balance: number;
        status: string;
        priority: string;
        end: Date;
      },
      // ... más contratos
    ],
    pagination: {
      page: number;
      limit: number;
      total: number;        // Total de registros que coinciden
      pages: number;        // Total de páginas
    }
  },
  error: null
}
```

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "commercial.contracts.search.list",
    "payload": {
      "page": 1,
      "limit": 20,
      "status": "OPEN",
      "company_id": 5
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "contracts": [
      {
        "id": 42,
        "reference": 1001,
        "limit": 10000,
        "balance": 7500,
        "status": "OPEN",
        "priority": "HIGH",
        "end": "2026-12-31T23:59:59Z"
      },
      {
        "id": 43,
        "reference": 1002,
        "limit": 5000,
        "balance": 5000,
        "status": "OPEN",
        "priority": "MEDIUM",
        "end": "2026-11-30T23:59:59Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 45,
      "pages": 3
    }
  },
  "error": null
}
```

---

## Validaciones

- ✅ Page >= 1
- ✅ Limit >= 1 y <= 1000
- ✅ Status es valor válido (si provided)
- ✅ Company existe (si provided)

---

## Documentos Relacionados

- [[contracts-search-all|Buscar Todo]] - Sin paginación
- [[_indice-funcionalidades|Índice]] - Todas las acciones

