---
tags: [servicios, TCP, commercial, mensajes, contratos]
última-revisión: 2026-04-27
---

# Mensajes TCP — MsCommercial

> **Tipo de comunicación:** Send (request/response)
> **Consumido por:** [[modulo-contracts]]
> **Fuente de patrones:** `src/common/cmd/constant.ts` → `CMDS.commercial`

## `commercial.contracts.create` — Send

**Propósito:** Crear un nuevo contrato comercial
**Patrón CMD:** `CMDS.commercial.contracts.create`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `IContractCreateContent` | Datos del contrato a crear |
| Salida | `TApi<{ id: number }>` | ID del contrato creado |

## `commercial.contracts.search.one` — Send

**Propósito:** Buscar un contrato por empresa y referencia
**Patrón CMD:** `CMDS.commercial.contracts.search.one`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ company: string, reference: string }` | Criterios de búsqueda |
| Salida | `TApi<IContractSearchOneResponse \| null>` | Contrato encontrado o null |

## `commercial.contracts.search.all` — Send

**Propósito:** Listar contratos con paginación
**Patrón CMD:** `CMDS.commercial.contracts.search.all`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `IContractSearchAllContent & IPagination` | Filtros + paginación |
| Salida | `TApi<IContractSearchAllResponse[]>` | Lista de contratos |

## `commercial.contracts.search.list` — Send

**Propósito:** Listar contratos por empresa, cliente y código
**Patrón CMD:** `CMDS.commercial.contracts.search.list`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ company, client, code } & IPagination` | Filtros + paginación |
| Salida | `TApi<IContractSearchListResponse[]>` | Lista de contratos |

## `commercial.contracts.search.reference` — Send

**Propósito:** Buscar contrato por referencia y empresa
**Patrón CMD:** `CMDS.commercial.contracts.search.reference`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ reference: string, company: string }` | Criterios de búsqueda |
| Salida | `TApi<IContractSearchReferenceResponse \| null>` | Contrato o null |

## `commercial.contracts.change.limit` — Send

**Propósito:** Modificar el límite de un contrato
**Patrón CMD:** `CMDS.commercial.contracts.change.limit`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ reference: string, company: string, limit: number }` | Identificación + nuevo límite |
| Salida | `TApi<{ balance: number, status: TCommercialContractStatus }>` | Balance y estado actualizados |

## `commercial.contracts.change.balance` — Send

**Propósito:** Modificar el balance de un contrato
**Patrón CMD:** `CMDS.commercial.contracts.change.balance`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ reference: string, company: string, balance: number }` | Identificación + nuevo balance |
| Salida | `TApi<{ status: TCommercialContractStatus }>` | Estado actualizado |
