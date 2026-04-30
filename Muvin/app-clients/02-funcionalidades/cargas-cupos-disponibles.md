# F-13 · Cupos Disponibles para Cargar

> **Módulo:** [modulo-cargas](../01-modulos/modulo-cargas.md)
> **Ruta:** `/cargaCuposDisponibles`

## Descripción

Lista de cupos ya asignados al usuario que están listos para ser cargados (estado = disponible para carga). El usuario selecciona uno para crear el pedido de transporte asociado.

## Endpoint

`GET v2/cupos/disponibles?userId=X&fecha=YYYY-MM-DD`

> 💀 Ruta v2. Verificar migración a v3.

## Flujo

1. Pantalla carga → `PedidoBloc.getCuposDisponibles(params)`.
2. Muestra lista de cupos con detalle (producto, cantidad, cabecera).
3. Tap en cupo → navega a `/cargaNuevoPedido` con el cupo preseleccionado.
