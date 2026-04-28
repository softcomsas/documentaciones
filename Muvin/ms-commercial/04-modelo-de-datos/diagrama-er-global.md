# Diagrama E-R Global

**Última actualización:** 2026-04-27

## Diagrama de Relaciones

```
┌─────────────────────────┐
│      entities           │
│ ─────────────────────── │
│ id (PK)                 │
│ cuit (UNIQUE, 11 chars) │
│ rs (name, UNIQUE)       │
└────────┬────────────────┘
         │
         │ FK (4 referencias)
         │
         ├──────────────────────┬──────────────────────┬──────────────────────┐
         │                      │                      │                      │
         ▼                      ▼                      ▼                      ▼
   ┌─────────────┐      ┌─────────────┐      ┌─────────────┐      ┌─────────────┐
   │contracts    │      │contracts    │      │contracts    │      │contracts    │
   │(company)    │      │(client)     │      │(broker)     │      │(destination)│
   └─────────────┘      └─────────────┘      └─────────────┘      └─────────────┘

     │
     │ FK (1 referencia)
     │
     ▼
┌──────────────────────────┐
│     products             │
│ ────────────────────────   │
│ id (PK)                  │
│ code (UNIQUE)            │
│ name (UNIQUE)            │
│ active (boolean)         │
└──────────────────────────┘

     │
     │ FK (1 referencia)
     │
     ▼
┌──────────────────────────┐
│     contracts            │
│ ────────────────────────   │
│ id (PK)                  │
│ reference (int)          │
│ company (FK→entities)    │
│ client (FK→entities)     │
│ broker (FK→entities, NK) │
│ destination (FK→entities)│
│ product (FK→products)    │
│ limit (int)              │
│ volume (int, NK)         │
│ price (Decimal)          │
│ balance (int)            │
│ status (OPEN|CLOSED|...) │
│ priority (enum)          │
│ start (DateTime)         │
│ end (DateTime)           │
│ createdAt (DateTime)     │
│ UNIQUE(reference, company)
└──────────────────────────┘

     │
     │ entity_roles (puente N:M)
     │
     ▼
┌──────────────────────────┐
│     roles                │
│ ────────────────────────   │
│ id (PK)                  │
│ description (UNIQUE)     │
└──────────────────────────┘

┌──────────────────────────┐
│     keys                 │
│ ────────────────────────   │
│ id (PK)                  │
│ key (UNIQUE, 64 chars)   │
│ secret (128 chars)       │
│ company (FK→entities)    │
│ active (boolean)         │
└──────────────────────────┘
```

---

## Relaciones Detalladas

### Contracts → Entities (4 FKs)

```
contracts.company_id     → entities.id  (empresa que ofrece)
contracts.client_id      → entities.id  (cliente que consume)
contracts.broker_id      → entities.id  (intermediario, NULLABLE)
contracts.destination_id → entities.id  (lugar de destino)
```

**Acción:** CASCADE on delete (si empresa se borra, sus contratos se borran)

---

### Contracts → Products (1 FK)

```
contracts.product_id → products.id  (producto en el contrato)
```

---

### Entity_Roles (N:M)

```
entity_roles.entity_id → entities.id
entity_roles.role_id   → roles.id

UNIQUE(entity_id, role_id)  ← Una entidad no puede tener el mismo rol dos veces
```

---

### Keys → Entities (1 FK)

```
keys.company_id → entities.id

UNIQUE(company_id)  ← Solo una API key por empresa
```

---

## Índices Principales

| Tabla | Índice | Columnas | Propósito |
|-------|--------|----------|-----------|
| contracts | PRIMARY | id | |
| contracts | UNIQUE | (reference, company_id) | Búsqueda rápida |
| contracts | INDEX | status | Filtrar por estado |
| contracts | INDEX | company_id | Filtrar por empresa |
| entities | PRIMARY | id | |
| entities | UNIQUE | cuit | Resolución CUIT → ID |
| entities | UNIQUE | rs | |
| products | PRIMARY | id | |
| products | UNIQUE | code | Búsqueda por código |
| products | UNIQUE | name | |
| keys | PRIMARY | id | |
| keys | UNIQUE | key | Validación de API key |
| keys | UNIQUE | company_id | |

---

## Constraints

| Constraint | Tabla | Descripción |
|-----------|-------|------------|
| PRIMARY KEY | todas | Clave primaria |
| FOREIGN KEY | contracts | Referencias a entities y products |
| UNIQUE | contracts | (reference, company) |
| UNIQUE | entities | cuit, rs |
| UNIQUE | products | code, name |
| UNIQUE | keys | key, company |
| NOT NULL | contracts | reference, company, client, destination, product, limit, balance, status |

---

## Ciclo de Vida de Datos

```
1. Entidad se crea (empresa, cliente)
   ↓
2. Producto se crea
   ↓
3. Contrato se crea (reference contrato)
   ↓
4. API key se genera para empresa
   ↓
5. Operaciones: buscar, actualizar
   ↓
6. Contrato se cierra (cuando balance=0 o manual)
   ↓
7. Datos permanecen para auditoría
```

---

## Documentos Relacionados

- [[_indice-entidades|Índice de Entidades]] - Guía general
- [[entidad-contracts|Contracts]] - Detalle de tabla
- [[entidad-entities|Entities]] - Detalle de tabla
- [[../05-inventarios/depends-matrix|Matriz de Dependencias]] - Impacto de cambios
- [[../07-operacion-y-despliegue/configuracion|Configuración]] - Connection string

