# Flujo: Creación de Contrato

**Última actualización:** 2026-04-27

## Diagrama de Secuencia

```
Cliente                ms-commercial         MySQL         ms-auth    ms-integrations
  │                         │                  │             │             │
  │ RPC: create contract     │                  │             │             │
  ├────────────────────────>│                  │             │             │
  │                         │                  │             │             │
  │                         │ Validar CUIT    │             │             │
  │                         ├─────────────────>│             │             │
  │                         │ Resultado        │             │             │
  │                         │<─────────────────┤             │             │
  │                         │                  │             │             │
  │                         │ Crear en BD      │             │             │
  │                         ├─────────────────>│             │             │
  │                         │ OK               │             │             │
  │                         │<─────────────────┤             │             │
  │                         │                  │             │             │
  │                         │ Notificar (async)             │             │
  │                         ├───────────────────────────────>│             │
  │                         │                  │             │             │
  │                         │ Registrar logs (async)         │             │
  │                         ├─────────────────────────────────────────────>│
  │                         │                  │             │             │
  │ Response: {id, ref...}  │                  │             │             │
  │<────────────────────────┤                  │             │             │
  │                         │                  │             │             │
```

---

## Pasos Detallados

### 1. Recibir RPC

```typescript
@MessagePattern('commercial.contracts.create')
async create(@Payload() payload: CreateContractDto)
```

Entrada validada en tipo TypeScript.

---

### 2. Validar Datos de Entrada

```typescript
// En ContractsService.createContract()
if (payload.reference < 0) throw ValidationException('...');
if (payload.start >= payload.end) throw ValidationException('...');
// ... más validaciones
```

---

### 3. Resolver CUITs a IDs

```typescript
const companyId = await this.entitiesRepository.findByCuit(payload.company_cuit);
if (!companyId) throw NotFoundException('Company not found');

const clientId = await this.entitiesRepository.findByCuit(payload.client_cuit);
// ... broker, destination
```

Query a tabla `entities`.

---

### 4. Validar Producto

```typescript
const productId = await this.productsRepository.findByCode(payload.product_code);
if (!productId) throw NotFoundException('Product not found');
```

Query a tabla `products`.

---

### 5. Validar Unicidad

```typescript
const exists = await this.contractsRepository.exists(companyId, payload.reference);
if (exists) throw ConflictException('Contract already exists');
```

Check en tabla `contracts`.

---

### 6. Crear en BD

```typescript
const contract = await this.contractsRepository.create({
  reference: payload.reference,
  company: companyId,
  client: clientId,
  broker: brokerId || null,
  destination: destinationId,
  product: productId,
  limit: payload.limit,
  balance: payload.limit,  // Balance inicial = limit
  price: payload.price,
  start: payload.start,
  end: payload.end,
  status: 'OPEN',
  priority: payload.priority || 'MEDIUM',
  createdAt: now(),
});
```

INSERT en tabla `contracts`.

---

### 7. Notificar ms-integrations (Async)

```typescript
// Fire and forget
this.integrationClient.emit('contract.created', {
  id: contract.id,
  company: companyId,
  // ... datos para email
});
```

---

### 8. Registrar en ms-logs (Async)

```typescript
// Fire and forget
this.logsClient.emit('contract.action', {
  action: 'CREATE',
  contract_id: contract.id,
  timestamp: now(),
});
```

---

### 9. Retornar Response

```typescript
return {
  success: true,
  data: {
    id: contract.id,
    reference: contract.reference,
    limit: contract.limit,
    balance: contract.balance,
  },
  error: null
};
```

JSON RPC response vuelve al cliente.

---

## Manejo de Errores

Si falla en cualquier paso de validación:

```typescript
catch (error) {
  return {
    success: false,
    data: null,
    error: error.message
  };
}
```

---

## Timing

| Paso | Duración |
|------|----------|
| Validaciones | 5-10ms |
| Queries entidades | 10-20ms |
| INSERT en DB | 10-30ms |
| Notificaciones async | No bloquea |
| Total | 50-100ms |

---

## Documentos Relacionados

- [[../02-funcionalidades/contracts-create|Create Contract]] - API detail
- [[../04-modelo-de-datos/entidad-contracts|Entidad Contracts]] - Schema
- [[_indice-flujos|Índice de Flujos]] - Otros flujos

