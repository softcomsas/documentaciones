# Entidad: Contracts

**Tabla:** `contracts`  
**Última actualización:** 2026-04-27

## Descripción

Tabla principal que almacena contratos comerciales entre empresa y cliente.

---

## Esquema Prisma

```prisma
model contracts {
  id           Int      @id @default(autoincrement())
  reference    Int
  company      entities @relation("company", fields: [company_id], references: [id])
  company_id   Int
  client       entities @relation("client", fields: [client_id], references: [id])
  client_id    Int
  broker       entities? @relation("broker", fields: [broker_id], references: [id])
  broker_id    Int?
  destination  entities @relation("destination", fields: [destination_id], references: [id])
  destination_id Int
  product      products @relation(fields: [product_id], references: [id])
  product_id   Int
  limit        Int
  volume       Int?
  price        Decimal  @db.Decimal(10, 2)
  balance      Int
  status       String   @db.Enum('OPEN', 'CLOSED', 'EXPIRED', 'VOIDED')
  priority     String   @db.Enum('HIGHEST', 'HIGH', 'MEDIUM', 'LOW', 'LOWEST')
  start        DateTime
  end          DateTime
  createdAt    DateTime @default(now())
  
  @@unique([reference, company_id])
  @@index([status])
  @@index([company_id])
}
```

---

## Columnas Detalladas

| Columna | Tipo | NULL | Descripción |
|---------|------|------|------------|
| id | INT | ✅ | PK, auto-increment |
| reference | INT | ❌ | Número de referencia del contrato |
| company_id | INT | ❌ | FK → entities (empresa proveedora) |
| client_id | INT | ❌ | FK → entities (cliente) |
| broker_id | INT | ✅ | FK → entities (intermediario, opcional) |
| destination_id | INT | ❌ | FK → entities (lugar destino) |
| product_id | INT | ❌ | FK → products |
| limit | INT | ❌ | Cupo máximo |
| volume | INT | ✅ | Volumen (opcional) |
| price | DECIMAL | ❌ | Precio por unidad |
| balance | INT | ❌ | Saldo disponible (>= 0) |
| status | ENUM | ❌ | OPEN\|CLOSED\|EXPIRED\|VOIDED |
| priority | ENUM | ❌ | HIGHEST\|HIGH\|MEDIUM\|LOW\|LOWEST |
| start | DATETIME | ❌ | Inicio de vigencia |
| end | DATETIME | ❌ | Fin de vigencia |
| createdAt | DATETIME | ❌ | Timestamp de creación |

---

## Validaciones

- `reference` > 0
- `limit` > 0
- `balance` >= 0
- `price` >= 0
- `volume` >= 0 (si no NULL)
- `start` < `end`
- Única combinación (reference, company_id)

---

## Estados Posibles

```
OPEN    → Contrato activo
CLOSED  → Contrato cerrado (manual o auto-cierre)
EXPIRED → Pasó fecha end
VOIDED  → Anulado
```

---

## Ejemplos de Datos

```sql
INSERT INTO contracts (reference, company_id, client_id, destination_id, product_id, 
                       limit, price, balance, status, priority, start, end)
VALUES 
  (1001, 5, 10, 20, 1, 10000, 100.50, 7500, 'OPEN', 'HIGH', '2026-01-01', '2026-12-31'),
  (1002, 5, 11, 21, 2, 5000, 50.00, 5000, 'OPEN', 'MEDIUM', '2026-02-01', '2026-11-30');
```

---

## Acceso vía Prisma

```typescript
// Crear
const contract = await prisma.contracts.create({
  data: {
    reference: 1001,
    company_id: 5,
    client_id: 10,
    destination_id: 20,
    product_id: 1,
    limit: 10000,
    balance: 10000,
    price: new Decimal('100.50'),
    status: 'OPEN',
    priority: 'MEDIUM',
    start: new Date('2026-01-01'),
    end: new Date('2026-12-31'),
  }
});

// Buscar uno
const contract = await prisma.contracts.findUnique({
  where: { id: 42 }
});

// Buscar por (reference, company)
const contract = await prisma.contracts.findFirst({
  where: {
    reference: 1001,
    company_id: 5,
  }
});

// Actualizar balance
const updated = await prisma.contracts.update({
  where: { id: 42 },
  data: { balance: 5000 }
});

// Listar con filtros
const contracts = await prisma.contracts.findMany({
  where: {
    status: 'OPEN',
    company_id: 5,
  },
  orderBy: { end: 'asc' },
  skip: (page - 1) * limit,
  take: limit,
});
```

---

## Performance Notes

- **Índice (reference, company_id):** Búsquedas rápidas O(1)
- **Índice status:** Filtrar por estado es rápido
- **Índice company_id:** Listar contratos por empresa es rápido

---

## Documentos Relacionados

- [[_indice-entidades|Índice de Entidades]] - Todas las tablas
- [[diagrama-er-global|Diagrama E-R]] - Relaciones
- [[entidad-entities|Entities]] - Tabla de entidades relacionadas
- [[../02-funcionalidades/contracts-create|Create Contract]] - Funcionalidad

