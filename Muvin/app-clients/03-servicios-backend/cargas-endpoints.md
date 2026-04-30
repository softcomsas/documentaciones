# Endpoints de Cargas y Pedidos

> [[_indice-servicios]] | Módulo: [[modulo-cargas]]

## GET `v2/cupos/disponibles`

> 💀 Endpoint legacy v2. Verificar migración a v3.

Cupos disponibles para ser cargados (con estado = listo para carga).

**Query params:** `userId`, `fecha` (YYYY-MM-DD)

**Response:** array de cupos con detalle de producto, cantidad, cabecera.

**BLoC:** `PedidoBloc.getCuposDisponibles()`

---

## POST `pedido`

Crea un nuevo pedido de transporte.

**Body:**
```json
{
  "cupoId": 100,
  "patente": "ABC123",
  "chofer": "Juan Pérez",
  "dni": "12345678",
  "cantidad": 30,
  "destino": "Puerto Rosario",
  "observaciones": ""
}
```

**Response:** objeto pedido con `id`, `estado`, `fechaCreacion`.

**BLoC:** `PedidoBloc.crearPedido()`

---

## POST `pedido/rapido`

Crea un pedido sin requerir cupo previamente asignado.

**Body:** mismo esquema que `POST pedido`, sin campo `cupoId`.

**BLoC:** `PedidoBloc.pedidoRapido()`

---

## GET `solicitud/:id`

Obtiene el detalle completo de una solicitud/pedido por ID.

**Path param:** `id` de la solicitud

**Response:** objeto con todos los campos de la solicitud incluyendo historial de estados.

**BLoC:** `PedidoBloc.getSolicitud()`
