# F-18 · Actualizaciones en Tiempo Real (Socket.IO)

> **Módulo:** [modulo-home](../01-modulos/modulo-home.md)

## Descripción

Conexión WebSocket persistente a `socket.muvinapp.com` iniciada al ingresar a `HomePage`. Permite recibir eventos del servidor sin polling.

## Configuración de conexión

```dart
final socket = io('https://socket.muvinapp.com', <String, dynamic>{
  'transports': ['websocket'],
  'autoConnect': false,
});
socket.connect();
```

## Eventos escuchados

| Evento | Acción en app |
|--------|--------------|
| `connect` | Log / indicador de conectado |
| `disconnect` | Log / indicador offline |
| `carga:update` | Recarga stream de PedidoBloc |
| `cupo:update` | Recarga stream de SolicitudCupoBloc |
| `notification` | Muestra Snackbar/notificación local |

## Riesgos

- ⚠️ No hay lógica de reconexión automática con backoff exponencial. Si el servidor reinicia, la conexión queda muerta.
- ⚠️ `socket_io_client ^0.9.x` es legacy. La API cambió significativamente en `^2.x`.
- ⚠️ URL del socket hardcodeada en `Config.urlWebSocket`.
