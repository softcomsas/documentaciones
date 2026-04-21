# Índice de Módulos — `muvin-ms-logs`

> **Última revisión:** 2026-04-21

## Módulos funcionales

| # | Módulo | Descripción breve | Criticidad | Enlace |
|---|--------|-------------------|-----------|--------|
| 1 | Microservices | Recibe y persiste trazas GraphQL y eventos de microservicios | 🔴 Alta | [[modulo-microservices]] |
| 2 | Legacy | Registra y consulta requests de los sistemas legados PANEL y DESCARGAS | 🔴 Alta | [[modulo-legacy]] |

## Módulos de infraestructura (no funcionales)

| Módulo | Propósito | Archivo |
|--------|-----------|---------|
| `AppModule` | Raíz — importa los dos módulos funcionales | `src/module.ts` |
| `PrismaService` | Conexión a MySQL vía Prisma Client | `src/service.ts` |

---

*Ver también: [[_indice-funcionalidades]] · [[arquitectura-alto-nivel]]*
