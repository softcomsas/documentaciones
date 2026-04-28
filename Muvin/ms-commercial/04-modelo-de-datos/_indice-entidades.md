# Índice de Entidades

**Última actualización:** 2026-04-27

## Entidades Principales

La base de datos **dev_commercial** contiene **5 tablas principales**:

```
┌──────────────────┐
│    contracts     │  → Contratos comerciales
├──────────────────┤
│  reference (int) │
│  company (FK)    │
│  client (FK)     │
│  broker (FK)     │
│  destination(FK) │
│  product (FK)    │
│  limit, balance  │
│  status, priority│
│  start, end      │
└──────────────────┘

┌──────────────────┐
│    entities      │  → Empresas/Clientes/Destinos
├──────────────────┤
│  cuit (UNIQUE)   │
│  rs (name)       │
└──────────────────┘

┌──────────────────┐
│    products      │  → Productos/Servicios
├──────────────────┤
│  code (UNIQUE)   │
│  name (UNIQUE)   │
│  active          │
└──────────────────┘

┌──────────────────┐
│      roles       │  → Roles de usuario
├──────────────────┤
│  description     │
└──────────────────┘

┌──────────────────┐
│  entity_roles    │  → Asignación de roles
├──────────────────┤
│  entity (FK)     │
│  role (FK)       │
└──────────────────┘

┌──────────────────┐
│      keys        │  → API keys para autenticación
├──────────────────┤
│  key (UNIQUE)    │
│  secret          │
│  company (FK)    │
│  active          │
└──────────────────┘
```

---

## Documentación por Entidad

| Entidad | Archivo | Descripción |
|---------|---------|------------|
| **Contracts** | [[entidad-contracts|entidad-contracts.md]] | Contratos comerciales |
| **Entities** | [[entidad-entities|entidad-entities.md]] | Empresas, clientes, destinos |
| **Products** | [[entidad-products|entidad-products.md]] | Productos/servicios |
| **Roles** | [[entidad-roles|entidad-roles.md]] | Roles de usuario |
| **Keys** | [[entidad-keys|entidad-keys.md]] | API keys |

---

## Diagrama E-R

Ver [[diagrama-er-global|Diagrama E-R Global]] para relaciones completas.

---

## Estadísticas

| Tabla | Columnas | Índices | Relaciones |
|-------|----------|---------|------------|
| contracts | 15+ | 3-5 | company, client, broker, destination, product |
| entities | 3 | 2 | CUIT, name |
| products | 4 | 2 | code, name |
| roles | 2 | 1 | description |
| entity_roles | 2 | 1 | entity, role |
| keys | 5 | 2 | key, company |

---

## Acceso a Datos

Todos los accesos van a través de **Prisma** (ORM tipado):

```typescript
// Crear contrato
const contract = await prisma.contracts.create({
  data: { ... }
});

// Buscar entidad
const entity = await prisma.entities.findUnique({
  where: { cuit: '20-12345678-9' }
});

// Listar productos activos
const products = await prisma.products.findMany({
  where: { active: true }
});
```

---

## Documentos Relacionados

- [[diagrama-er-global|Diagrama E-R Global]] - Relaciones visuales
- [[../01-modulos/_indice-modulos|Módulos NestJS]] - Repositorios
- [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades]] - Uso desde API

