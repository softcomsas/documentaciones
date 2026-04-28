# Entidad: Products

**Tabla:** `products`  
**Última actualización:** 2026-04-27

## Descripción

Almacena catálogo de productos o servicios que pueden ser ofrecidos en contratos.

---

## Esquema Prisma

```prisma
model products {
  id       Int         @id @default(autoincrement())
  code     Int         @unique
  name     String      @unique
  active   Boolean     @default(true)
  
  contracts contracts[]
}
```

---

## Columnas

| Columna | Tipo | NULL | Descripción |
|---------|------|------|------------|
| id | INT | ✅ | PK, auto-increment |
| code | INT | ❌ | Código único del producto |
| name | VARCHAR | ❌ | Nombre del producto (UNIQUE) |
| active | BOOLEAN | ❌ | Si está disponible (defecto: true) |

---

## Validaciones

- `code` es UNIQUE
- `name` es UNIQUE
- `active` es boolean
- Todos NOT NULL

---

## Ejemplos de Datos

```sql
INSERT INTO products (code, name, active) VALUES
  (1, 'Producto A', true),
  (2, 'Producto B', true),
  (3, 'Producto C (Discontinued)', false);
```

---

## Acceso vía Prisma

```typescript
// Crear
const product = await prisma.products.create({
  data: {
    code: 1,
    name: 'Producto A',
    active: true,
  }
});

// Buscar por código
const product = await prisma.products.findUnique({
  where: { code: 1 }
});

// Buscar por nombre
const product = await prisma.products.findUnique({
  where: { name: 'Producto A' }
});

// Listar solo activos
const active = await prisma.products.findMany({
  where: { active: true }
});

// Actualizar estado
const updated = await prisma.products.update({
  where: { id: 1 },
  data: { active: false }
});
```

---

## Documentos Relacionados

- [[_indice-entidades|Índice de Entidades]] - Todas las tablas
- [[entidad-contracts|Contracts]] - Usa productos
- [[../02-funcionalidades/contracts-create|Create Contract]] - Valida producto

