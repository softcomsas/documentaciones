# F-14 · Nuevo Pedido de Transporte

> **Módulo:** [modulo-cargas](../01-modulos/modulo-cargas.md)
> **Ruta:** `/cargaNuevoPedido`

## Descripción

Formulario de alta de un pedido de transporte vinculado a un cupo seleccionado. El usuario completa los datos del camión, chofer y destino. Al confirmar se crea el pedido y se asocia el entregador al cupo.

## Campos del formulario

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Cupo | Heredado | Cupo seleccionado en pantalla anterior |
| Patente camión | Texto | Identificación del vehículo |
| Nombre chofer | Texto | |
| DNI chofer | Numérico | |
| Destino | Selector | |
| Cantidad (tn) | Numérico | |
| Observaciones | Texto libre | Opcional |

## Endpoints

| Acción | Endpoint |
|--------|----------|
| Crear pedido | `POST pedido` |
| Asociar entregador al cupo | `PUT cupo/:id` |

Ver [cargas-endpoints](../03-servicios-backend/cargas-endpoints.md).

## Riesgos

- ⚠️ Son dos llamadas HTTP secuenciales (POST pedido + PUT cupo). Si la segunda falla, el pedido queda huérfano sin cupo asociado.
