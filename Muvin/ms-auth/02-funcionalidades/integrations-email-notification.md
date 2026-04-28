# Funcionalidad: Enviar Notificación por Email

> **Módulo:** [[modulo-integrations]]
> **CMD:** `integrations.email.notification`
> **Tipo:** Integración / RPC emit (fire-and-forget)

## Descripción funcional

Dispara el envío de un email de notificación a través del microservicio de integraciones. Se utiliza para informar a usuarios o administradores sobre eventos relevantes del sistema de autenticación (por ejemplo: creación de nueva clave API, acceso denegado reiterado).

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `email` | `string` | Sí | Dirección de email del destinatario |
| `history` | `unknown` | Sí | Datos del historial/evento a notificar (⚠️ Estructura pendiente de verificar) |

## Respuesta

Sin respuesta (emit — fire-and-forget).

## Tipos relacionados

`IJobInternalNotification` en `src/common/interfaces/jobs/internal/notification.ts`:
```typescript
{ email: string, history: unknown }
```

## Riesgos

- ⚠️ El campo `history` es `unknown` — no hay contrato tipado del contenido del email.
- ⚠️ Sin confirmación de envío — si `ms-integrations` falla, la notificación se pierde.
- ⚠️ No hay mecanismo de reintento ni cola de mensajes fallidos.

## Archivos fuente relevantes

- `src/contracts/integrations/interfaces/email.ts` (interfaz `notification`)
- `src/common/interfaces/jobs/internal/notification.ts`
- `src/common/cmd/interfaces/integrations.ts`
