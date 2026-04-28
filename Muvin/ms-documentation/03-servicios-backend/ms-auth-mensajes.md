---
tags: [servicios, TCP, auth, mensajes]
última-revisión: 2026-04-27
---

# Mensajes TCP — MsAuth

> **Tipo de comunicación:** Send (request/response)
> **Consumido por:** [[modulo-contracts]]
> **Fuente de patrones:** `src/common/cmd/constant.ts` → `CMDS.auth`

## `auth.companies.search.one`

**Propósito:** Buscar una empresa por ID
**Patrón CMD:** `CMDS.auth.companies.search.one`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `string` | ID de la empresa |
| Salida | `TApi<SchemeAuthCompany>` | Datos de la empresa |

## `auth.companies.search.all`

**Propósito:** Buscar múltiples empresas por IDs
**Patrón CMD:** `CMDS.auth.companies.search.all`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `string[]` | Array de IDs de empresas |
| Salida | `TApi<Record<string, SchemeAuthCompany>>` | Mapa id → empresa |

## `auth.create.key`

**Propósito:** Crear una clave de API para un usuario
**Patrón CMD:** `CMDS.auth.validate.create`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `number` | ID del usuario |
| Salida | `TApi<{ key: string, secret: string }>` | Clave y secreto generados |

## `auth.generate.signature`

**Propósito:** Generar una firma para autenticación
**Patrón CMD:** `CMDS.auth.validate.generate`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `string` | Datos a firmar |
| Salida | `TApi<{ signature: string, timestamp: number }>` | Firma y timestamp |

## `auth.validate.key`

**Propósito:** Validar una clave de API con su firma
**Patrón CMD:** `CMDS.auth.validate.key`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `{ key: string, signature: string, timestamp: number }` | Credenciales a validar |
| Salida | `TApi<number>` | ID del usuario si válido |

## `auth.validate.authorization`

**Propósito:** Validar si un usuario tiene los scopes requeridos
**Patrón CMD:** `CMDS.auth.validate.authorization`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `string \| string[]` | Scope(s) a verificar |
| Salida | `TApi<boolean>` | `true` si autorizado |

## `auth.validate.legacy`

**Propósito:** Validar acceso a sistemas legacy
**Patrón CMD:** `CMDS.auth.validate.legacy`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Entrada | `string \| string[]` | Credencial(es) legacy |
| Salida | `TApi<boolean>` | `true` si válido |
