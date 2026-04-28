# Endpoints: Integrations (consumidos)

> **Microservicio destino:** ms-integrations
> **Transporte:** TCP
> **Última revisión:** 2026-04-27

---

## `integrations.email.notification`

**Propósito:** Enviar notificación por email
**Tipo:** RPC emit (fire-and-forget) | **Detalle:** [[integrations-email-notification]]

| Campo | Tipo | Obligatorio |
|-------|------|-------------|
| `email` | `string` | Sí |
| `history` | `unknown` | Sí |

**Efectos secundarios:** Dispara envío de email en ms-integrations (Gmail / SMTP).
