# Índice de Funcionalidades — `muvin-ms-logs`

> **Última revisión:** 2026-04-21

## Módulo Microservices

| # | Funcionalidad | Tipo | Pattern TCP | Enlace |
|---|---------------|------|-------------|--------|
| 1.1 | Crear traza | Integración write | `logs.trace.create` | [[microservices-trace-create]] |
| 1.2 | Actualizar traza | Integración write | `logs.trace.update` | [[microservices-trace-update]] |
| 1.3 | Crear evento | Integración write | `logs.event.create` | [[microservices-event-create]] |
| 1.4 | Actualizar evento | Integración write | `logs.event.create` ⚠️ BUG | [[microservices-event-update]] |

## Módulo Legacy

| # | Funcionalidad | Tipo | Pattern TCP | Enlace |
|---|---------------|------|-------------|--------|
| 2.1 | Crear registro legacy | Integración write | `logs.legacy.create` | [[legacy-create]] |
| 2.2 | Actualizar registro legacy | Integración write | `logs.legacy.update` | [[legacy-update]] |
| 2.3 | Buscar por ID | CRUD read | `logs.legacy.search.id` | [[legacy-search-id]] |
| 2.4 | Buscar por usuario | CRUD read paginado | `logs.legacy.search.user` | [[legacy-search-user]] |
| 2.5 | Buscar por términos | Búsqueda semántica | `logs.legacy.search.terms` | [[legacy-search-terms]] |

---

*Ver también: [[_indice-modulos]] · [[message-patterns-endpoints]]*
