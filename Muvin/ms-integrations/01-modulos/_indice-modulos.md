# Índice de Módulos

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Módulos del sistema

| # | Módulo | Descripción breve | Criticidad | Enlace |
|---|---|---|---|---|
| 1 | Core | Infraestructura global: Prisma, Bull, repositories | 🔴 Alta | [[modulo-core]] |
| 2 | Gmail | Integración con Gmail API: watch + notificaciones | 🔴 Alta | [[modulo-gmail]] |

---

> [!info] Microservicio de propósito único
> Este repositorio contiene exactamente **dos módulos NestJS**. El módulo `Core` es infraestructura pura (@Global), y el módulo `Gmail` es el único módulo de negocio activo.

---

## Ver también

- [[arquitectura-alto-nivel]]
- [[cross-module-dependencies]]
- [[_indice-funcionalidades]]
