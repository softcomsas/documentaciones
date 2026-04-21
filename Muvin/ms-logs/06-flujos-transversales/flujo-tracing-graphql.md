# Flujo: Tracing de Operación GraphQL

> **Contexto:** [[_indice-flujos]] · [[modulo-microservices]]
> **Actores:** Gateway (NestJS interceptor), ms-logs (TCP receiver), MySQL

## Descripción

Una operación GraphQL completa genera hasta **N+2 mensajes TCP** hacia ms-logs:
- 1 mensaje `trace.create` al inicio
- N mensajes `event.create` (uno por cada microservicio llamado durante la resolución)
- N mensajes `event.update` (uno por respuesta de cada MS — 🔴 BUG: nunca llegan al handler correcto)
- 1 mensaje `trace.update` al finalizar

## Diagrama de secuencia — Estado diseñado

```mermaid
sequenceDiagram
    actor U as Usuario
    participant GW as Gateway (NestJS)
    participant ML as ms-logs (TCP)
    participant MSx as Microservicio X
    participant DB as MySQL

    U->>GW: query { operacion(...) }
    GW->>ML: emit('logs.trace.create', {hash, operation, type, payload, user})
    ML->>DB: INSERT traces (status=PENDING)

    GW->>ML: emit('logs.event.create', {trace:hash, hash:event1, service:'ms-x', payload})
    ML->>DB: INSERT events
    GW->>MSx: emit(cmd, payload)
    MSx-->>GW: response

    GW->>ML: emit('logs.event.update', {hash:event1, trace:hash, response})
    Note over ML: ⚠️ BUG — 'logs.event.update' no existe como CMD
    Note over ML: El mensaje se descarta silenciosamente

    GW-->>U: GraphQL response
    GW->>ML: emit('logs.trace.update', {hash, response, status:'SUCCESS'})
    ML->>DB: UPDATE traces (status, response, finishedAt, duration)
```

## Diagrama de secuencia — Estado real actual

```mermaid
sequenceDiagram
    actor U as Usuario
    participant GW as Gateway (NestJS)
    participant ML as ms-logs (TCP)
    participant DB as MySQL

    U->>GW: query { operacion(...) }
    GW->>ML: emit('logs.trace.create', payload)
    ML->>DB: INSERT traces (status=PENDING)

    GW->>ML: emit('logs.event.create', payload)
    ML->>DB: INSERT events (response=null, finishedAt=null)

    Note over GW,ML: emit('logs.event.update') → handler incorrecto → NO-OP

    GW->>ML: emit('logs.trace.update', payload)
    ML->>DB: UPDATE traces (COMPLETED)

    Note over DB: events.response, events.duration, events.finishedAt = null FOREVER
```

## Correlación entre entidades

| Campo | Valor | Une con |
|-------|-------|---------|
| `traces.hash` | Correlation ID de la traza | `events.trace` |
| `events.hash` | Correlation ID del evento individual | — (propio) |
| `events.trace` | Mismo valor que `traces.hash` | Agrupa todos los eventos de una traza |

## Uso esperado del hash

El hash es generado por el gateway **antes de enviar los mensajes**, típicamente como UUID o hash del request. No existe validación de formato en `ms-logs` — cualquier string de hasta 50 chars es aceptado.

## Estado actual de la tabla events

```
events:
  response    → NULL (100% de los registros)
  duration    → NULL (100% de los registros)
  finishedAt  → NULL (100% de los registros)
```

Ver [[microservices-event-update]] y [[deuda-tecnica]] para el fix.

---

*Ver también: [[flujo-legacy-logging]] · [[entidad-traces]] · [[entidad-events]]*
