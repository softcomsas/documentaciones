# Índice de Servicios Backend — api-sockets

> [[../README]]

## Interfaces expuestas

api-sockets expone **dos interfaces** simultáneas:

1. **WebSocket (Socket.IO v2)** — puerto `:5000` (upgrade desde HTTP)
2. **REST HTTP (Express)** — puerto `:5000` (mismo servidor HTTP)

## Endpoints REST

| Método | Ruta | Auth | Descripción |
|--------|------|------|-------------|
| GET | `/borrar-usuarios` | ❌ | Resetea Redis |
| GET | `/mensajes` | ❌ | Healthcheck |
| POST | `/mensajes` | ❌ | Broadcast mensaje-nuevo |
| POST | `/mensajes/:id` | ❌ | Mensaje privado por socket ID |
| GET | `/usuarios` | ❌ | Lista usuarios desde Redis |
| GET | `/usuarios/detalle` | ❌ | Lista usuarios in-memory |
| POST | `/consulta` | ❌ | Emite nueva-consulta a consultores |
| POST | `/chofer-respuesta-consulta/:id` | ❌ | Emite respuesta-consulta al chofer |
| GET | `/chofer-tienes-viaje/:id` | ❌ | Emite tienes-viaje al chofer |
| POST | `/chofer/:id` | ❌ | Mensaje genérico a chofer |
| POST | `/notificacion` | ❌ | Notificación por id_persona |

> Detalle completo: [[rest-endpoints]]

## Eventos Socket.IO

| Evento entrante | Descripción | Evento saliente |
|-----------------|-------------|-----------------|
| `connection` | Nueva conexión | — |
| `configurar-usuario` | Registrar identidad | `configurar-usuario`, `usuario-conectado` |
| `obtener-usuarios` | Solicitar lista | `usuarios-activos` |
| `mensaje` | Broadcast mensaje | `mensaje-nuevo` |
| `disconnect` | Desconexión | — |

> Detalle completo: [[socket-events]]

## Detalle

- [[rest-endpoints]] — Todos los endpoints REST con payloads y respuestas
- [[socket-events]] — Todos los eventos Socket.IO con diagramas
