# Inventario de Reportes y Wizards

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Resultado del inventario

> [!info] Sin reportes ni wizards
> Este microservicio **no contiene reportes, wizards ni formularios** de ningún tipo. Es un servicio de integración de fondo (_background integration service_) sin interfaz de usuario propia.

---

## Justificación

| Tipo | Cantidad | Motivo |
|---|---|---|
| Reportes (PDF, Excel, HTML) | 0 | No aplica — sin capa de presentación |
| Wizards / asistentes multi-paso | 0 | No aplica — sin HTTP REST/frontend |
| Formularios web | 0 | No aplica — solo protocolo TCP |
| Dashboards | 0 | No aplica |
| Exportaciones programadas | 0 | No aplica |

---

## Salidas que sí genera el sistema

Aunque no hay reportes, el sistema produce los siguientes artefactos de datos:

| Artefacto | Destino | Descripción |
|---|---|---|
| Registro en `gmail_messages` | Base de datos MySQL | Cada email procesado queda persistido con estado `PENDING` |
| Job en cola Bull | Redis (`email` queue) | Payload con datos de autenticación y mensaje para procesamiento externo |
| Logs de consola | Stdout (NestJS Logger) | Trazas de inicialización, errores y eventos |

---

## Ver también

- [[functional-classification]]
- [[modulo-gmail]]
- [[flujo-notificacion-gmail]]
