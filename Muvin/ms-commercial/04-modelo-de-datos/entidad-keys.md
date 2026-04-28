# Entidad: Keys

**Tabla:** `keys`  
**Última actualización:** 2026-04-27

## Descripción

Almacena API keys y secrets para autenticación de clientes RPC.

---

## Esquema Prisma

```prisma
model keys {
  id        Int      @id @default(autoincrement())
  key       String   @unique @db.VarChar(64)
  secret    String   @db.VarChar(128)
  company   entities @relation(fields: [company_id], references: [id])
  company_id Int     @unique
  active    Boolean  @default(true)
}
```

---

## Columnas

| Columna | Tipo | NULL | Descripción |
|---------|------|------|------------|
| id | INT | ✅ | PK, auto-increment |
| key | VARCHAR(64) | ❌ | API key (hexadecimal), UNIQUE |
| secret | VARCHAR(128) | ❌ | API secret (hexadecimal) |
| company_id | INT | ❌ | FK → entities |
| active | BOOLEAN | ❌ | Si está habilitada (defecto: true) |

---

## Validaciones

- `key` es UNIQUE
- `key` tiene 64 caracteres
- `secret` tiene 128 caracteres
- `company_id` es UNIQUE (1 key por empresa)
- Todos NOT NULL

---

## Ejemplos de Datos

```sql
INSERT INTO keys (key, secret, company_id, active) VALUES
  ('a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3', 'x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3n5o7p9q2r4s6t8u1v3w5x7y9z', 5, true),
  ('f1e2d3c4b5a697a8b9c0d1e2f3a4b5c6', 'z9y8x7w6v5u4t3s2r1q0p9o8n7m6l5k4j3i2h1g0f9e8d7c6b5a4', 6, true);
```

---

## Acceso vía Prisma

```typescript
// Crear
const key = await prisma.keys.create({
  data: {
    key: 'a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3',
    secret: 'x9y2z4a6b8c1d3e5f7g9h2i4j6k8l1m3n5o7p9q2r4s6t8u1v3w5x7y9z',
    company_id: 5,
    active: true,
  }
});

// Buscar por key
const key = await prisma.keys.findUnique({
  where: { key: 'a7c3e1f9b2d6f4a8c5e2b9d1f7a4c6e3' }
});

// Buscar por empresa
const key = await prisma.keys.findUnique({
  where: { company_id: 5 }
});

// Desactivar
const updated = await prisma.keys.update({
  where: { id: 1 },
  data: { active: false }
});
```

---

## Seguridad

> [!warning]
> El `secret` NO se guarda en plaintext. En producción, debe ser:
> - Encriptado en reposo (DB encryption)
> - Never retornado en queries posteriores
> - Retornado UNA SOLA VEZ en create-key

---

## Documentos Relacionados

- [[_indice-entidades|Índice de Entidades]] - Todas las tablas
- [[entidad-entities|Entities]] - Empresas que tienen keys
- [[../02-funcionalidades/validation-create-key|Create API Key]] - Funcionalidad

