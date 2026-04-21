# Índice de Módulos

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21

---

## Módulos funcionales

| # | Módulo | Descripción breve | Criticidad | Estado | Enlace |
|---|--------|------------------|:----------:|:------:|--------|
| 1 | Email Processor | Consume jobs Bull para procesar correos de Gmail con adjuntos PDF | 🔴 Alta | Activo (incompleto) | [[modulo-email]] |
| 2 | Common | Utilidades compartidas, interfaces de jobs, contratos de tipos | 🟡 Media | Activo (con dead code) | [[modulo-common]] |
| 3 | Config | Validación de entorno y definición de colas | 🔴 Alta | Activo | [[modulo-config]] |
| 4 | Services | Servicios inyectables (PdfParserService) | 🟡 Media | Activo | [[modulo-services]] |

---

## Módulos declarados pero sin implementación

| Módulo | Descripción | Estado |
|--------|-------------|--------|
| Internal Processor | Cola `internal.notification` definida en `queues.ts` e interface en `common` pero sin `@Processor` implementado | 🚧 Sin implementar |
