# F-12 · Gestión de Cargas Principal

> **Módulo:** [modulo-cargas](../01-modulos/modulo-cargas.md)
> **Ruta:** `/cargas`

## Descripción

Landing del módulo de cargas. Muestra el estado de cargas activas del usuario: cupos en proceso de carga, pedidos creados y su estado. Permite navegar a "Cupos disponibles para cargar" o iniciar un "Pedido rápido".

## Secciones de la pantalla

| Sección | Descripción |
|---------|-------------|
| Cargas en curso | Lista de pedidos activos con estado en tiempo real |
| Acceso a cupos disponibles | Botón → `/cargaCuposDisponibles` |
| Pedido rápido | Botón → flujo `pedido/rapido` |

## Actualización en tiempo real

Los estados de carga se actualizan vía Socket.IO. El widget escucha el evento `carga:update` del socket y recarga el stream de `PedidoBloc`.
