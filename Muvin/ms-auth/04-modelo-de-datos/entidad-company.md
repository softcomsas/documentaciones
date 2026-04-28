# Entidad: Company

> **BD:** ms-auth (MySQL local)
> **Fuente del contrato:** `src/contracts/auth/schema.ts` — `ICompany`
> **Fuente Prisma:** `prisma/schema.prisma` (⚠️ campos exactos pendientes de verificar)

---

## Descripción

Representa una empresa u organización registrada en el ecosistema Muvin. Es la entidad raíz de autenticación — toda clave API pertenece a una compañía.

---

## Campos (según contrato TypeScript)

| Campo | Tipo | Nulable | Descripción |
|-------|------|---------|-------------|
| `id` | `number` | No | Identificador único (PK) |
| `cuit` | `string` | No | CUIT/CUIL de la empresa (identificador fiscal argentino) |
| `rs` | `string` | No | Razón social de la empresa |

> [!warning] Campos adicionales posibles
> El esquema Prisma puede tener más campos (created_at, updated_at, active, etc.) que no están expuestos en el contrato TypeScript.

---

## Relaciones

- Una `Company` puede tener múltiples `Key` → [[entidad-key]]

---

## Módulos que la consumen

- [[modulo-auth]] — búsqueda de compañías, validación de claves
- [[modulo-commercial]] — referenciada en contratos comerciales (indirectamente)

---

## Archivos fuente relevantes

- `src/contracts/auth/schema.ts` (interfaz `ICompany`)
- `prisma/schema.prisma` (modelo Prisma — verificar)
