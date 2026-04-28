---
tags: [servicios, TCP, integrations, mensajes, email]
última-revisión: 2026-04-27
---

# Mensajes TCP — MsIntegrations

> **Tipo de comunicación:** Emit (fire and forget)
> **Consumido por:** [[modulo-contracts]]
> **Fuente de patrones:** `src/common/cmd/constant.ts` → `CMDS.integrations`

## `integrations.email.notification` — Emit

**Propósito:** Enviar una notificación por email
**Patrón CMD:** `CMDS.integrations.email.notification`
**Tipo:** Fire and forget (no espera confirmación)

| Campo | Obligatorio | Tipo | Descripción |
|-------|:-----------:|------|-------------|
| `email` | ✅ | `string` | Dirección de email destinataria |
| `history` | ✅ | `number` | ID del historial/registro asociado a la notificación |

> [!info] Uso típico
> Este mensaje se emite cuando se necesita notificar a un usuario sobre un evento del sistema (ej: cambio de estado de un contrato, resultado de una operación). `MsIntegrations` se encarga de construir el contenido del email y despacharlo.
