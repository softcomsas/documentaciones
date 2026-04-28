# Entidad: Entities

**Tabla:** `entities`  
**Última actualización:** 2026-04-27

## Descripción

Almacena información de empresas, clientes, corredores y destinos en el sistema. Cada entidad se identifica por su CUIT (Código Único de Identificación Tributaria).

---

## Esquema Prisma

```prisma
model entities {
  id           Int      @id @default(autoincrement())
  cuit         String   @unique @db.VarChar(11)
  rs           String   @unique  // razón social (nombre)
  
  // Relaciones inversas
  company_contracts      contracts[] @relation("company")
  client_contracts       contracts[] @relation("client")
  broker_contracts       contracts[] @relation("broker")
  destination_contracts  contracts[] @relation("destination")
  
  entity_roles entity_roles[]
  keys         keys[]
}
```

---

## Columnas

| Columna | Tipo | NULL | Descripción |
|---------|------|------|------------|
| id | INT | ✅ | PK, auto-increment |
| cuit | VARCHAR(11) | ❌ | CUIT (XX-XXXXXXXX-X), UNIQUE |
| rs | VARCHAR | ❌ | Razón social (nombre), UNIQUE |

---

## Validaciones

- `cuit` tiene 11 caracteres (formato: XX-XXXXXXXX-X)
- `cuit` es UNIQUE
- `rs` es UNIQUE
- Ambos campos NOT NULL

---

## Ejemplos de Datos

```sql
INSERT INTO entities (cuit, rs) VALUES
  ('20-12345678-9', 'Empresa Ejemplo S.A.'),
  ('30-98765432-1', 'Cliente XYZ Ltd.'),
  ('27-44444444-4', 'Corredor ABC'),
  ('27-11111111-1', 'Destino Principal');
```

---

## Acceso vía Prisma

```typescript
// Crear
const entity = await prisma.entities.create({
  data: {
    cuit: '20-12345678-9',
    rs: 'Empresa Ejemplo S.A.',
  }
});

// Buscar por CUIT
const entity = await prisma.entities.findUnique({
  where: { cuit: '20-12345678-9' }
});

// Buscar por razón social
const entity = await prisma.entities.findUnique({
  where: { rs: 'Empresa Ejemplo S.A.' }
});

// Listar todas
const all = await prisma.entities.findMany();

// Con relaciones
const entityWithContracts = await prisma.entities.findUnique({
  where: { id: 5 },
  include: {
    company_contracts: true,      // Contratos donde es empresa
    client_contracts: true,        // Contratos donde es cliente
    broker_contracts: true,        // Contratos donde es corredor
    destination_contracts: true,   // Contratos donde es destino
  }
});
```

---

## Rol de Entidad en Contratos

Una entidad puede participar en un contrato de 4 formas:

| Rol | Relación | Descripción |
|-----|----------|------------|
| **Company** | contracts.company_id | Empresa que ofrece/provee |
| **Client** | contracts.client_id | Cliente que consume |
| **Broker** | contracts.broker_id | Intermediario (opcional) |
| **Destination** | contracts.destination_id | Lugar de destino/entrega |

---

## Performance Notes

- **Índice UNIQUE cuit:** Búsqueda por CUIT es O(1)
- **Índice UNIQUE rs:** Búsqueda por razón social es O(1)

---

## Documentos Relacionados

- [[_indice-entidades|Índice de Entidades]] - Todas las tablas
- [[diagrama-er-global|Diagrama E-R]] - Relaciones
- [[entidad-contracts|Contracts]] - Tabla de contratos
- [[../01-modulos/modulo-contracts|Módulo Contracts]] - Resolución CUIT

