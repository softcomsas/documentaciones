# Endpoints: Commercial (consumidos)

> **Microservicio destino:** ms-commercial
> **Transporte:** TCP
> **Última revisión:** 2026-04-27

> [!info] ms-auth como consumidor
> ms-auth invoca estos comandos hacia ms-commercial. La implementación de los handlers está en ms-commercial, no en este repositorio.

---

## `commercial.contracts.create`

**Propósito:** Crear un nuevo contrato comercial
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-create]]

| Campo entrada | Tipo | Obligatorio |
|---------------|------|-------------|
| `reference` | `string` | Sí |
| `company` | `number` | Sí |
| `client` | `number` | Sí |
| `broker` | `number` | Sí |
| `destination` | `number` | Sí |
| `code` | `number` | Sí |
| `limit` | `number` | Sí |
| `volume` | `number` | Sí |
| `price` | `number` | Sí |
| `start` | ⚠️ pendiente | Sí |
| `end` | ⚠️ pendiente | Sí |
| `status` | `TCommercialContractStatus` | No |
| `priority` | `TCommercialContractPriority` | No |

---

## `commercial.contracts.search.one`

**Propósito:** Buscar contrato por company + reference
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-search-one]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `company` | `number` | Sí |
| `reference` | `string` | Sí |

---

## `commercial.contracts.search.list`

**Propósito:** Listar contratos por company + client + code
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-search-list]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `company` | `number` | Sí |
| `client` | `number` | Sí |
| `code` | `number` | Sí |

---

## `commercial.contracts.search.reference`

**Propósito:** Buscar contratos por reference + company
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-search-reference]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `reference` | `string` | Sí |
| `company` | `number` | Sí |

---

## `commercial.contracts.search.all`

**Propósito:** Listado paginado con filtros opcionales
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-search-all]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `company` | `number` | Sí |
| `page` | `number` | Sí |
| `limit` | `number` | Sí |
| `client` | `number` | No |
| `status` | `TCommercialContractStatus` | No |
| `code` | `number` | No |

**Respuesta:** `{ data: IContract[], meta: { total: number } }`

---

## `commercial.contracts.change.limit`

**Propósito:** Cambiar el límite de un contrato
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-change-limit]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `company` | `number` | Sí |
| `reference` | `string` | Sí |
| `limit` | `number` | Sí |

---

## `commercial.contracts.change.balance`

**Propósito:** Cambiar el balance de un contrato
**Tipo:** RPC send | **Detalle:** [[commercial-contracts-change-balance]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `company` | `number` | Sí |
| `reference` | `string` | Sí |
| `balance` | `number` | Sí |
