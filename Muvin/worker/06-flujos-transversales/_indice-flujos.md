# Índice de Flujos Transversales

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21

---

## Flujos documentados

| # | Flujo | Descripción | Detalle |
|---|-------|-------------|---------|
| 1 | Procesamiento de Certificado de Transferencia | Flujo completo desde publicación del job hasta (ausente) persistencia | [[flujo-procesamiento-certificado]] |

---

## Observación general

Este worker tiene un único flujo de negocio activo. El 100% del procesamiento ocurre como consecuencia de jobs publicados en la cola Bull `email`, proceso `email.pdf`. No hay endpoints HTTP, cron jobs, ni eventos de sistema que disparen lógica adicional.
