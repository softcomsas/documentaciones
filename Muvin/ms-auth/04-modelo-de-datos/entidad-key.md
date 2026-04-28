# Entidad: Key

> **BD:** ms-auth (MySQL local)
> **Fuente del contrato:** `src/contracts/auth/schema.ts` — `IKey`
> **Fuente Prisma:** `prisma/schema.prisma` (⚠️ campos exactos pendientes de verificar)

---

## Descripción

Representa un par de credenciales API (key pública + secret privado) asignado a una compañía. Es el mecanismo central de autenticación del ecosistema.

---

## Campos (según contrato TypeScript)

| Campo | Tipo | Nulable | Descripción |
|-------|------|---------|-------------|
| `id` | `number` | No | Identificador único (PK) |
| `key` | `string` | No | Clave pública (se envía en cada request) |
| `secret` | `string` | No | Secreto privado (usado para generar firmas — nunca viaja en requests) |
| `company` | `number` | No | FK a Company |
| `active` | `boolean` | No | Si la clave está habilitada para autenticación |

---

## Relaciones

- Pertenece a una `Company` → [[entidad-company]]

---

## Riesgos de seguridad

- 🔴 `secret` debe estar hasheado en base de datos. Sin implementación no es verificable.
- 🔒 No hay TTL ni fecha de expiración en el contrato — las claves son permanentes hasta desactivación manual.
- 🔒 No hay mecanismo de rotación automática de claves.
- ⚠️ `active` es el único control de revocación disponible.

---

## Módulos que la consumen

- [[modulo-auth]] — creación, búsqueda y validación de claves

---

## Archivos fuente relevantes

- `src/contracts/auth/schema.ts` (interfaz `IKey`, `IAuth`)
- `prisma/schema.prisma` (modelo Prisma — verificar)
