# Funcionalidad: Cambiar Balance

**Pattern:** `commercial.contracts.change.balance`  
**Módulo:** Contracts  
**Tipo:** WRITE  
**Última actualización:** 2026-04-27

## Descripción

Actualiza el balance (saldo disponible) de un contrato. **AUTO-CIERRE:** si el nuevo balance es 0, el contrato se cierra automáticamente (status = CLOSED).

---

## Entrada

```typescript
{
  contract_id: number;
  new_balance: number;    // Nuevo balance (>= 0)
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    id: number;
    balance: number;      // Nuevo balance
    status: string;       // OPEN o CLOSED (si balance=0)
  },
  error: null
}
```

---

## Validaciones

- ✅ Contract existe
- ✅ new_balance >= 0
- ✅ new_balance no puede ser negativo

---

## Ejemplo 1: Cambiar Balance (Contrato sigue OPEN)

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "commercial.contracts.change.balance",
    "payload": {
      "contract_id": 42,
      "new_balance": 5000
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "balance": 5000,
    "status": "OPEN"
  },
  "error": null
}
```

---

## Ejemplo 2: Auto-Cierre (balance = 0)

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "commercial.contracts.change.balance",
    "payload": {
      "contract_id": 42,
      "new_balance": 0        // ← Dispara auto-cierre
    }
  }'
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 42,
    "balance": 0,
    "status": "CLOSED"        // ← Automáticamente cerrado
  },
  "error": null
}
```

---

## ⚠️ AUTO-CIERRE (CRÍTICO)

> [!danger]
> **Cuando balance = 0, el contrato se cierra automáticamente.**
> - Status cambia a CLOSED
> - Esta acción es IRREVERSIBLE
> - No se puede reabrir un contrato cerrado
> - Esto se registra para auditoría

---

## Diferencia con change.limit

| Acción | Afecta | Efecto |
|--------|--------|--------|
| change.limit | Cupo máximo | No cierra contrato |
| change.balance | Saldo disponible | Auto-cierra si = 0 |

---

## Documentos Relacionados

- [[contracts-change-limit|Cambiar Cupo]] - Actualizar limite
- [[contracts-create|Crear Contrato]] - Balance inicial
- [[_indice-funcionalidades|Índice]] - Todas las acciones

