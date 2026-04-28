# Endpoints: Auth

> **Microservicio:** ms-auth (este servicio)
> **Transporte:** TCP
> **Última revisión:** 2026-04-27

---

> [!warning] Handlers no implementados
> Los comandos aquí documentados tienen contratos tipados pero carecen de handler RPC. El microservicio no procesa estos mensajes actualmente.

---

## `auth.companies.search.one`

**Propósito:** Buscar una compañía por su ID
**Tipo:** RPC send (request/response)
**Consumido por:** Consumidores externos (API Gateway)
**Detalle:** [[auth-companies-search-one]]

### Payload de entrada

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `id` | `number` | Sí |

### Respuesta

| Código | Significado | Estructura |
|--------|-------------|------------|
| success: true | Encontrado | `{ id, cuit, rs }` |
| success: false | No encontrado | `{ code, message }` |

---

## `auth.companies.search.all`

**Propósito:** Buscar múltiples compañías por IDs
**Tipo:** RPC send (request/response)
**Detalle:** [[auth-companies-search-all]]

### Payload de entrada

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `ids` | `number[]` | Sí |

### Respuesta

| Código | Estructura |
|--------|------------|
| success: true | `ICompany[]` |

---

## `auth.create.key`

**Propósito:** Crear par key/secret para una compañía
**Tipo:** RPC send (request/response)
**Detalle:** [[auth-validate-create-key]]

### Payload de entrada

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `company` | `number` | Sí |

### Respuesta

| success | Estructura |
|---------|------------|
| true | `{ id, key, secret, company, active }` |

### Efectos secundarios

- INSERT en tabla de claves (MySQL vía Prisma)

---

## `auth.generate.signature`

**Propósito:** Generar firma criptográfica para un request
**Tipo:** RPC send (request/response)
**Detalle:** [[auth-validate-generate-signature]]

### Payload de entrada

⚠️ Pendiente de verificar — ver `src/contracts/auth/interfaces/validation.ts`

### Respuesta

| success | Estructura |
|---------|------------|
| true | `{ signature: string }` |

---

## `auth.validate.key`

**Propósito:** Validar clave API + firma + timestamp
**Tipo:** RPC send (request/response)
**Detalle:** [[auth-validate-key]]

### Payload de entrada

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `key` | `string` | Sí |
| `signature` | `string` | Sí |
| `timestamp` | `number` | Sí |

### Respuesta

| success | Estructura |
|---------|------------|
| true | `IAuth: { company: ICompany, key: IKey }` |
| false | `{ code, message }` |

---

## `auth.validate.authorization`

**Propósito:** Validar autorización de una entidad autenticada
**Tipo:** RPC send (request/response)
**Detalle:** [[auth-validate-authorization]]

### Payload de entrada

⚠️ Pendiente de verificar — ver `src/contracts/auth/interfaces/validation.ts`

---

## `auth.validate.legacy`

**Propósito:** Validar autenticación compatible con sistema legacy
**Tipo:** RPC send (request/response)
**Detalle:** [[auth-validate-legacy]]

### Payload de entrada

⚠️ Pendiente de verificar — ver `src/contracts/auth/interfaces/validation.ts`
