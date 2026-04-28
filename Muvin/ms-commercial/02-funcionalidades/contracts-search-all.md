# Funcionalidad: Buscar Contratos (Todo)

**Pattern:** `commercial.contracts.search.all`  
**Módulo:** Contracts  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Busca todos los contratos que coincidan con los filtros, SIN paginación. Útil para exportar o analizar todos los datos.

---

## Entrada

```typescript
{
  status?: string;        // Filtro opcional
  company_id?: number;    // Filtro opcional
  // Sin paginación (page, limit)
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
        // ... más campos
      },
      // ...
    ]
  },
  error: null
}
```

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "commercial.contracts.search.all",
    "payload": {
      "status": "OPEN"
    }
  }'
```

---

## ⚠️ Consideraciones

> [!warning]
> SIN paginación: si hay muchos contratos, puede retornar lista muy grande. Usar con cuidado en producción.

---

## Documentos Relacionados

- [[contracts-search-list|Buscar Lista]] - Con paginación (RECOMENDADO)
- [[_indice-funcionalidades|Índice]] - Todas las acciones

