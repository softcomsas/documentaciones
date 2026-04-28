# Índice: Servicios Backend

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

> [!info] Microservicio TCP — sin endpoints HTTP
> `ms-auth` no expone endpoints HTTP/REST. Toda la comunicación es mediante **mensajes TCP RPC** con NestJS Microservices. Este índice documenta los comandos TCP disponibles como si fueran "endpoints", organizados por dominio.

---

## Comandos expuestos por ms-auth (proveedor)

Estos son los mensajes que `ms-auth` **recibe y debe procesar**:

| Dominio | Archivo de detalle |
|---------|-------------------|
| Auth — Companies | [[auth-endpoints]] |
| Auth — Validation | [[auth-endpoints]] |

## Comandos consumidos por ms-auth (consumidor)

Estos son los mensajes que `ms-auth` **envía a otros microservicios**:

| Dominio | Microservicio destino | Archivo de detalle |
|---------|----------------------|-------------------|
| Commercial — Contracts | ms-commercial | [[commercial-endpoints]] |
| Logs — Legacy | ms-logs | [[logs-endpoints]] |
| Integrations — Email | ms-integrations | [[integrations-endpoints]] |
