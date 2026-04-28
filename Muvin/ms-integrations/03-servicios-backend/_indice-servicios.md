# Índice de Servicios Backend

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Servicios externos consumidos

| Servicio | Tipo | Enlace |
|---|---|---|
| Gmail API (Google) | REST API externa | [[gmail-api-endpoints]] |
| Redis / Bull Queue | Cola de mensajes | Documentado en [[modulo-core]] |
| MySQL | Base de datos relacional | Ver [[diagrama-er-global]] |

---

## Contratos de microservicio (mensajes TCP)

Este microservicio **expone** los siguientes message patterns hacia el ecosistema:

| Pattern | Payload | Respuesta | Enlace |
|---|---|---|---|
| `integrations.email.notification` | `{email: string, history: number}` | `void` | [[gmail-notification]] |

---

## Contratos declarados pero no implementados

Los siguientes contratos están definidos como tipos en `src/contracts/` pero no tienen implementación activa en este microservicio:

| Contrato | Tipo | Estado |
|---|---|---|
| `logs.legacy.create` | emit | 💀 Solo tipos |
| `logs.legacy.update` | emit | 💀 Solo tipos |
| `logs.legacy.search.id` | send | 💀 Solo tipos |
| `commercial.contracts.create` | send | 💀 Solo tipos |
| `commercial.contracts.search.reference` | send | 💀 Solo tipos |
| `commercial.contracts.search.all` | send | 💀 Solo tipos |

---

## Ver también

- [[gmail-api-endpoints]]
- [[modulo-gmail]]
- [[modulo-core]]
