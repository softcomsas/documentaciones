# Visión General — api-sockets

> [[arquitectura-alto-nivel]] | [[stack-tecnologico]] | [[glosario]]

## ¿Qué es api-sockets?

`api-sockets` es el **servidor de comunicación en tiempo real** del ecosistema Muvin. Provee dos interfaces:

1. **WebSocket (Socket.IO v2):** Los clientes (apps Angular/Flutter) se conectan y reciben/emiten eventos en tiempo real.
2. **REST HTTP (Express):** Los servicios backend (api-panel, api) llaman endpoints HTTP para enviar notificaciones o consultas a usuarios específicamente identificados.

## Responsabilidades

| # | Responsabilidad | Mecanismo |
|---|-----------------|-----------|
| 1 | Gestionar presencia de usuarios conectados | In-memory (`UsuariosLista`) + Redis |
| 2 | Broadcast de mensajes a todos los clientes | `io.emit('mensaje-nuevo')` |
| 3 | Mensajes privados a un socket específico | `io.in(id).emit(...)` |
| 4 | Notificaciones a usuarios por `id_persona` | REST POST `/notificacion` + Redis lookup |
| 5 | Despacho de consultas a choferes | REST POST `/consulta` → emit `nueva-consulta` |
| 6 | Respuesta de consulta a chofer específico | REST POST `/chofer-respuesta-consulta/:id` |
| 7 | Notificar viaje disponible a un chofer | REST GET `/chofer-tienes-viaje/:id_chofer` |

## Posición en el sistema

api-sockets **no tiene base de datos propia** (excepto Redis para presencia). No autentica usuarios por JWT — confía en que el cliente declare su `id_persona` al enviar `configurar-usuario`.

## Puerto por defecto

`5000` (configurable via variable de entorno `PORT`)
