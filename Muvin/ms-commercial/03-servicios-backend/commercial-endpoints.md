# Endpoints Comerciales

**Última actualización:** 2026-04-27

## Contratos Comerciales - API Contracts

Los siguientes contratos definen la interfaz de los endpoints comerciales.

---

## Contrato: CommercialContracts

**Ubicación:** `src/contracts/commercial/contract.ts`

Define los tipos compartidos entre clientes y servidor:

```typescript
export namespace CommercialContracts {
  // Namespaces anidados para organización
  export namespace Contracts {
    // Create
    export interface CreatePayload { ... }
    export interface CreateResponse { ... }
    
    // Search
    export interface SearchOnePayload { ... }
    export interface SearchOneResponse { ... }
    // ... más operaciones
  }
}
```

---

## Interfaces Principales

### CreateContract

```typescript
interface CreatePayload {
  reference: number;
  company_cuit: string;
  client_cuit: string;
  broker_cuit?: string;
  destination_cuit: string;
  product_code: number;
  limit: number;
  volume?: number;
  price: Decimal;
  start: Date;
  end: Date;
  priority?: string;
}

interface CreateResponse {
  id: number;
  reference: number;
  limit: number;
  balance: number;
  status: 'OPEN';
}
```

### SearchOne

```typescript
interface SearchOnePayload {
  reference: number;
  company_cuit: string;
}

interface SearchOneResponse {
  id: number;
  reference: number;
  limit: number;
  balance: number;
  status: string;
}
```

---

## MessagePattern Mapping

| Pattern | Payload | Response |
|---------|---------|----------|
| commercial.contracts.create | CreatePayload | {id, reference, limit, balance} |
| commercial.contracts.search.one | SearchOnePayload | {id, reference, limit, balance, status} |
| commercial.contracts.search.list | SearchListPayload | {contracts[], pagination} |
| commercial.contracts.search.all | SearchAllPayload | {contracts[]} |
| commercial.contracts.search.reference | SearchRefPayload | {contract completo} |
| commercial.contracts.change.limit | ChangeLimitPayload | {id, limit} |
| commercial.contracts.change.balance | ChangeBalancePayload | {id, balance, status} |

---

## Uso de Contrato en Cliente

```typescript
// Cliente (otro microservicio)
import { CommercialContracts } from 'src/contracts';

const payload: CommercialContracts.Contracts.CreatePayload = {
  reference: 1001,
  company_cuit: '20-12345678-9',
  // ...
};

const response = await client.send<CommercialContracts.Contracts.CreateResponse>(
  'commercial.contracts.create',
  payload
);
```

---

## Esquema Prisma Asociado

**Ubicación:** `src/contracts/commercial/schema.ts`

Valida payloads antes de procesar:

```typescript
import { z } from 'zod';

export const CreatePayloadSchema = z.object({
  reference: z.number().positive(),
  company_cuit: z.string().regex(/^\d{2}-\d{8}-\d$/),
  // ... más validaciones
});
```

---

## Documentos Relacionados

- [[_indice-servicios|Índice de Servicios]] - Visión general
- [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades]] - Uso desde cliente
- [[../04-modelo-de-datos/_indice-entidades|Modelo de Datos]] - Entidades subyacentes

