# Índice de Servicios Backend (APIs Externas)

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21

---

## Resumen de integraciones externas

| # | API | Base URL | Autenticación | Módulo consumidor | Estado | Detalle |
|---|-----|----------|--------------|------------------|:------:|---------|
| 1 | Gmail API v1 | `https://gmail.googleapis.com/gmail/v1` | JWT / Domain-wide Delegation | [[modulo-email]] | ✅ Activo | [[gmail-endpoints]] |

> [!note] Sin API propia
> Este worker **no expone endpoints HTTP**. Solo consume APIs externas. `NestFactory.create()` se usa sin `app.listen()` — es un proceso headless de queue.

---

## Tecnologías de integración

| Dependencia | Versión | Rol |
|------------|---------|-----|
| `googleapis` | ^166.0.0 | Cliente oficial Google APIs (Gmail v1) |
| `bull` | ^4.16.5 | Broker de jobs Redis (consume jobs del API Muvin) |
