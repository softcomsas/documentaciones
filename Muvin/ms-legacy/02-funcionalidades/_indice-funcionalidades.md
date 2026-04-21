# Índice de funcionalidades

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Funcionalidades implementadas

| # | Módulo | Funcionalidad | Tipo | Estado | Detalle |
|---|--------|---------------|------|--------|---------|
| 1 | [[modulo-api]] | Búsqueda de compradores por razón social | Integración / Proxy | 🟢 Activo | [[api-comprador-by-razon-social]] |

## Funcionalidades declaradas pero no implementadas

| # | Módulo | Funcionalidad | Tipo | Estado | Nota |
|---|--------|---------------|------|--------|------|
| 2 | [[modulo-api]] | Búsqueda de compradores por CUIT | Integración / Proxy | ⚠️ Pendiente | Endpoint declarado en `TEndpoint`, sin implementar |

> [!info] Alcance del microservicio
> `muvin-ms-legacy` tiene un scope intencionalmente acotado. No es un sistema de funcionalidades múltiples; es un **proxy de integración** con una sola funcionalidad activa. La extensión se hace agregando archivos en `src/api/queries/` y registrando la query en `QUERIES_MAP`.
