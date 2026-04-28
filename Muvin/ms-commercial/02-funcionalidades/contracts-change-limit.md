# Funcionalidad: Cambiar Cupo

**Pattern:** `commercial.contracts.change.limit`  
**Módulo:** Contracts  
**Tipo:** WRITE  
**Última actualización:** 2026-04-27

## Descripción

Actualiza el cupo (limit) de un contrato. El contrato permanece en su estado actual. Esta acción es independiente del balance.

---

## Entrada

```typescript
{
  contract_id: number;
  new_limit: number;      // Nuevo cupo (debe ser > 0)
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    id: number;
    limit: number;        // Nuevo limit
  },
  error: null
}
```

---

## Validaciones

- ✅ Contract existe
- ✅ new_limit > 0

---

## Ejemplo

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "commercial.contracts.change.limit",
    "payload": {
      "contract_id": 42,
      "new_limit": 15000
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "limit": 15000
  },
  "error": null
}
```

---

## Nota Importante

> [!info]
> **Cambiar limit NO cambia balance.** Si el nuevo limit es menor que el balance, el balance permanece igual pero será > limit (deuda).

Ejemplo:
- Contrato original: limit=10000, balance=7000
- Cambio: new_limit = 5000
- Resultado: limit=5000, balance=7000 (balance > limit!)

---

## Documentos Relacionados

- [[contracts-change-balance|Cambiar Balance]] - Para actualizar saldo
- [[contracts-create|Crear Contrato]] - Para crear con limit inicial
- [[_indice-funcionalidades|Índice]] - Todas las acciones

