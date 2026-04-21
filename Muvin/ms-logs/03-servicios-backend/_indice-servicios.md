# Índice: Servicios Backend

> **Sección:** Servicios Backend
> **Contexto:** [[arquitectura-alto-nivel]] · [[_indice-modulos]]

## Resumen

`ms-logs` expone **9 message patterns TCP** (no endpoints HTTP). Los patrones se dividen en dos grupos según el módulo que los atiende.

## Tabla de patrones por módulo

| # | Pattern TCP | Handler | Módulo | Returns |
|---|-------------|---------|--------|---------|
| 1 | `logs.trace.create` | `createTrace()` | [[modulo-microservices]] | void |
| 2 | `logs.trace.update` | `updateTrace()` | [[modulo-microservices]] | void |
| 3 | `logs.event.create` | `createEvent()` | [[modulo-microservices]] | void |
| 4 | `logs.event.update` ⚠️ BUG | `updateEvent()` | [[modulo-microservices]] | void |
| 5 | `logs.legacy.create` | `create()` | [[modulo-legacy]] | void |
| 6 | `logs.legacy.update` | `update()` | [[modulo-legacy]] | void |
| 7 | `logs.legacy.search.id` | `searchId()` | [[modulo-legacy]] | `TLogLegacy \| null` |
| 8 | `logs.legacy.search.user` | `searchUser()` | [[modulo-legacy]] | `IApiResponse<T[]>` |
| 9 | `logs.legacy.search.terms` | `searchTerms()` | [[modulo-legacy]] | `TLogLegacy[]` |

> ⚠️ Pattern #4 nunca es invocado correctamente — ver [[microservices-event-update]] y [[deuda-tecnica]].

## Documentación detallada

- [[message-patterns-endpoints]]

---

*Ver también: [[_indice-modulos]] · [[_indice-funcionalidades]]*
