# Flujos Transversales — Índice

> **Última revisión:** 2026-04-29

Flujos que atraviesan múltiples capas o módulos de muvin-api.

---

## Flujos documentados

| Flujo | Descripción | Archivo |
|-------|-------------|---------|
| F-TX-01 | Ciclo de vida completo de un request REST | [[flujo-request-rest]] |
| F-TX-02 | Ciclo de vida completo de una query GraphQL | [[flujo-request-graphql]] |
| F-TX-03 | Comunicación con microservicios (TCP) | [[flujo-microservicios]] |

---

## Diagrama global de flujos

```mermaid
flowchart TD
    C[Cliente] -->|HTTP| MW[Global Enhancers\nValidationPipe · AllExceptionsFilter\nGraphqlLoggingInterceptor]
    MW --> R{Tipo de request}
    R -->|POST /api/graphql| GQL[GraphQlModule\nLegacyResolver]
    R -->|REST /api/logs| REST1[LogsModule\nLogsController]
    R -->|REST /api/descargas| REST2[TemporaryModule\nTemporaryController]

    GQL -->|TCP send| MS_LEG[ms-legacy]
    REST1 -->|TCP emit/send| MS_LOG[ms-logs]
    REST2 -->|HTTPS Axios\n2-step auth| DESCARGAS[panel.muvinapp.com]

    MS_LEG & MS_LOG & DESCARGAS -->|response| C
```
