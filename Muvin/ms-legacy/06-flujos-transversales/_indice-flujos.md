# Índice de flujos transversales

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Flujos documentados

| # | Flujo | Descripción | Tipo | Detalle |
|---|-------|-------------|------|---------|
| 1 | Request proxy end-to-end | Ciclo de vida completo de un mensaje TCP desde el consumidor hasta el backend legacy y vuelta | Secuencia | [[flujo-request-proxy]] |
| 2 | Arranque del microservicio | Bootstrap, validación de entorno y registro del listener TCP | Proceso | [[flujo-bootstrap]] |

> [!info] Flujos de este microservicio
> Por ser un proxy de integración, solo existe un flujo de negocio principal (request → proxy → response). No hay flujos de autenticación propios, ni procesos batch, ni eventos asincrónicos internos.
