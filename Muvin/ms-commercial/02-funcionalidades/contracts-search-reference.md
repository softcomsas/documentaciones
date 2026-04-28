# Funcionalidad: Buscar por Reference (Datos Completos)

**Pattern:** `commercial.contracts.search.reference`  
**Módulo:** Contracts  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Busca un contrato por reference + company_id y retorna TODOS los datos completos (no solo básicos), incluyendo información de entidades relacionadas.

---

## Entrada

```typescript
{
  reference: number;
  company_id: number;
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    id: number;
    reference: number;
    company: {
      id: number;
      cuit: string;
      name: string;
    };
    client: {
      id: number;
      cuit: string;
      name: string;
    };
    broker?: {
      id: number;
      cuit: string;
      name: string;
    };
    destination: {
      id: number;
      cuit: string;
      name: string;
    };
    product: {
      id: number;
      code: number;
      name: string;
    };
    limit: number;
    balance: number;
    volume?: number;
    price: Decimal;
    status: string;
    priority: string;
    start: Date;
    end: Date;
    createdAt: Date;
  },
  error: null
}
```

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "commercial.contracts.search.reference",
    "payload": {
      "reference": 1001,
      "company_id": 5
    }
  }'
```

---

## Diferencia con search.one

| Aspecto | search.one | search.reference |
|--------|-----------|------------------|
| Entrada | reference + CUIT | reference + company_id |
| Salida | Datos básicos | Datos completos |
| Entidades | No | Sí, con details |
| Uso | Búsqueda rápida | Necesitar todos los datos |

---

## Documentos Relacionados

- [[contracts-search-one|Buscar Uno]] - Datos básicos, busca por CUIT
- [[_indice-funcionalidades|Índice]] - Todas las acciones

