# Entidad: Roles

**Tabla:** `roles`  
**Última actualización:** 2026-04-27

## Descripción

Almacena roles de usuario para control de acceso basado en roles (RBAC).

---

## Esquema Prisma

```prisma
model roles {
  id           Int      @id @default(autoincrement())
  description  String   @unique
  
  entity_roles entity_roles[]
}
```

---

## Columnas

| Columna | Tipo | NULL | Descripción |
|---------|------|------|------------|
| id | INT | ✅ | PK, auto-increment |
| description | VARCHAR | ❌ | Nombre del rol (UNIQUE) |

---

## Ejemplos de Roles

```sql
INSERT INTO roles (description) VALUES
  ('Admin'),
  ('Manager'),
  ('Viewer'),
  ('Editor');
```

---

## Acceso vía Prisma

```typescript
// Crear
const role = await prisma.roles.create({
  data: { description: 'Admin' }
});

// Buscar
const role = await prisma.roles.findUnique({
  where: { description: 'Admin' }
});

// Listar
const all = await prisma.roles.findMany();
```

---

## Documentos Relacionados

- [[_indice-entidades|Índice de Entidades]] - Todas las tablas
- [[entidad-entities|Entities]] - Tabla de entidades que tienen roles

