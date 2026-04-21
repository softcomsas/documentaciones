# Entidad: events

> **Contexto:** [[_indice-entidades]] · [[modulo-microservices]]
> **Tabla MySQL:** `events`
> **Propósito:** Registro de llamadas individuales a microservicios dentro de una traza GraphQL

## Campos

| Campo | Tipo Prisma | Tipo MySQL | Nullable | Default | Descripción |
|-------|-------------|------------|----------|---------|-------------|
| `id` | `Int` | INT | No | auto | PK |
| `trace` | `String` | VARCHAR(50) | No | — | Hash de la traza padre (sin FK) |
| `hash` | `String` | VARCHAR(50) | No | — | Correlation ID del evento (UK) |
| `service` | `String` | VARCHAR(30) | No | — | Nombre del MS llamado |
| `duration` | `Int?` | INT | Sí | null | Duración en ms (null hasta update — 🔴 nunca se actualiza) |
| `createdAt` | `DateTime` | DATETIME | No | `now()` | Timestamp de creación |
| `finishedAt` | `DateTime?` | DATETIME | Sí | null | Timestamp de finalización (null — 🔴 nunca se actualiza) |
| `payload` | `Json` | JSON | No | — | Mensaje enviado al MS |
| `response` | `Json?` | JSON | Sí | null | Respuesta del MS (null — 🔴 nunca se actualiza) |

## Índices

| Índice | Campos | Tipo |
|--------|--------|------|
| `events_hash_key` | `hash` | UNIQUE |
| `events_createdAt_idx` | `createdAt` | simple |
| `events_trace_createdAt_idx` | `trace`, `createdAt` | compuesto |
| `events_hash_createdAt_idx` | `hash`, `createdAt` | compuesto |
| `events_service_createdAt_idx` | `service`, `createdAt` | compuesto |

## Ciclo de vida (diseñado vs. real)

```mermaid
stateDiagram-v2
    state "Diseño" {
        [*] --> Abierto : event.create
        Abierto --> Cerrado : event.update (🔴 BUG — nunca ocurre)
    }
    state "Realidad actual" {
        [*] --> AbiertoPermanente : event.create
        note right of AbiertoPermanente : response/duration/finishedAt\nsiempre null
    }
```

## ⚠️ Estado crítico

Debido al bug en `src/common/cmd/constant.ts` (ver [[microservices-event-update]] y [[deuda-tecnica]]):

- `response` siempre es `null` en todos los registros de producción
- `duration` siempre es `null`
- `finishedAt` siempre es `null`

Esta tabla registra únicamente **el inicio** de las llamadas a microservicios. Los datos de observabilidad (latencias, respuestas) están perdidos.

## Relaciones

- `events.trace` referencia `traces.hash` (sin FK en BD — solo por convención de aplicación)

---

*Ver también: [[entidad-traces]] · [[microservices-event-create]] · [[microservices-event-update]] · [[deuda-tecnica]]*
