---
tags: [servicios, TCP, inter-microservicios]
última-revisión: 2026-04-27
---

# Índice de servicios backend (mensajes TCP)

> [!info] Sin HTTP
> Este microservicio no expone endpoints REST. La comunicación es 100% vía mensajes TCP usando el sistema de microservicios de NestJS (`ClientProxy.send()` / `ClientProxy.emit()`).

## Microservicios del ecosistema que este MS consume

| Microservicio | Tipo de comunicación | Patrones disponibles | Enlace |
|---------------|---------------------|---------------------|--------|
| MsAuth | Send (request/response) | 7 patrones | [[ms-auth-mensajes]] |
| MsLogs | Emit + Send | 5 patrones | [[ms-logs-mensajes]] |
| MsCommercial | Send (request/response) | 7 patrones | [[ms-commercial-mensajes]] |
| MsIntegrations | Emit (fire and forget) | 1 patrón | [[ms-integrations-mensajes]] |

> [!warning] Contratos definidos, clientes no implementados
> Los contratos TCP están tipados en `src/contracts/` y los patrones de mensajes en `src/common/cmd/constant.ts`, pero los `ClientProxy` que los invocan **no están implementados aún** en módulos de dominio.

## Mensajes que este MS puede recibir

> [!warning] Sin handlers implementados
> No hay ningún `@MessagePattern` registrado. El microservicio escucha en TCP pero no responde a ningún mensaje. Los handlers se documentarán aquí cuando se implementen.
