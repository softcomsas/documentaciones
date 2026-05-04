# Módulo: Router REST

> [[_indice-modulos]] | Archivo: `routes/router.ts`

## Propósito

Expone endpoints HTTP que permiten a los **servicios backend** (api-panel, api) interactuar con los clientes WebSocket conectados sin necesidad de implementar Socket.IO ellos mismos.

Usa:
- `Server.instance.io` para emitir eventos Socket.IO
- `usuariosConectados` (in-memory) para buscar choferes/consultores
- Redis para buscar por `id_persona` y limpiar la lista

## Endpoints definidos

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/borrar-usuarios` | Resetea `listaUsuarios` en Redis a `[]` |
| GET | `/mensajes` | Healthcheck — devuelve `{ok:true}` |
| POST | `/mensajes` | Broadcast de `mensaje-nuevo` a todos |
| POST | `/mensajes/:id` | Mensaje privado `mensaje-privado` a socket por ID |
| GET | `/usuarios` | Lista de usuarios desde Redis |
| GET | `/usuarios/detalle` | Lista in-memory (`usuariosConectados.getLista()`) |
| POST | `/consulta` | Emite `nueva-consulta` a todos los usuarios con `consultas=true` |
| POST | `/chofer-respuesta-consulta/:id_chofer` | Emite `respuesta-consulta` al chofer |
| GET | `/chofer-tienes-viaje/:id_chofer` | Emite `tienes-viaje: true` al chofer |
| POST | `/chofer/:id_chofer` | Emite `mensaje-privado` al chofer |
| POST | `/notificacion` | Emite `mensaje-privado` a lista de `id_persona` |

> Ver detalle completo de payloads y respuestas en [[rest-endpoints]].

## CORS

```typescript
router.use(cors())
```
CORS habilitado sin restricción de origen en todas las rutas del router. Se suma al CORS global de `index.ts`.

## Autenticación

**Ningún endpoint tiene autenticación.** Cualquier cliente HTTP puede llamar estos endpoints.
