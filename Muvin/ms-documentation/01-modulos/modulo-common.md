---
tags: [módulo, utilidades, common]
última-revisión: 2026-04-27
---

# Módulo: common (Utilidades compartidas)

> **Ruta/Namespace:** `src/common/`
> **Criticidad:** 🟡 Media
> **Estado:** Activo

## Propósito

Conjunto de utilidades reutilizables: constantes de message patterns, helpers de logging y respuesta estandarizada, e interfaces compartidas. No es un módulo NestJS; es una librería TypeScript pura accesible vía el alias `@common`.

## Componentes

### `cmd/constant.ts` — CMDS

Objeto centralizado con todos los strings de message patterns del ecosistema. **Es la única fuente de verdad para los patrones de mensajes**. Nunca usar strings literales: siempre referenciar `CMDS.xxx`.

| Dominio | Ejemplos de patrones |
|---------|---------------------|
| `auth` | `auth.companies.search.one`, `auth.validate.key`, `auth.validate.authorization` |
| `logs` | `logs.legacy.create`, `logs.legacy.update`, `logs.legacy.search.id` |
| `integrations` | `integrations.email.notification` |
| `commercial` | `commercial.contracts.create`, `commercial.contracts.search.one`, `commercial.contracts.change.balance` |

Ver listado completo de contratos en [[modulo-contracts]].

### `functions/`

| Función | Firma | Descripción |
|---------|-------|-------------|
| `LOG` | `(value: string, data?: unknown) => void` | Logger con colores ANSI. Resalta `[...]` en amarillo, resto en verde. |
| `IDENTITY` | `<T>(x: T): T` | Función identidad genérica. |
| `successResponseFn` | `<T>(data: T, total?: number) => TApiSuccessResponse<T>` | Construye respuesta exitosa estándar. |
| `errResponseFn` | `(code: number, message) => TApiErrorResponse` | Construye respuesta de error estándar. |

### `interfaces/`

| Tipo | Descripción |
|------|-------------|
| `TApi<T>` | Union discriminada: `TApiSuccessResponse<T> \| TApiErrorResponse` |
| `TApiSuccessResponse<T>` | `{ success: true, data: T }` con `meta.total` opcional para paginación |
| `TApiErrorResponse` | `{ success: false, error: { code: number, message: string \| string[] \| Record<string,string[]> } }` |
| `IOption<T>` | `{ value: T, label: string, disabled: boolean }` |
| `IOptionExtended<T>` | `IOption<T>` + `{ id: number }` |
| `IPagination` | `{ page: number, limit: number }` |
| `IJobEmailPdf` | Job de email con PDF adjunto (id, gmail, entity, auth) |
| `IJobInternalNotification` | `{ email: string, history: number }` |

## Archivos fuente relevantes

- `src/common/cmd/constant.ts`
- `src/common/functions/api-response.ts`
- `src/common/functions/logger.ts`
- `src/common/functions/identity.ts`
- `src/common/interfaces/api-response.ts`
- `src/common/interfaces/pagination.ts`
- `src/common/interfaces/jobs/email/pdf.ts`
- `src/common/interfaces/jobs/internal/notification.ts`
