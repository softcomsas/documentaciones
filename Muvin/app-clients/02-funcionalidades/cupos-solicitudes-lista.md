# F-09 · Listado de Mis Solicitudes

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **Ruta:** interna → `SolicitudesPage`

## Descripción

Muestra las solicitudes de cupo enviadas por el usuario autenticado, filtradas por fecha. Cada tarjeta muestra: producto, fecha, cantidad solicitada y estado. Tap en tarjeta → [F-10 Detalle de solicitud](./cupos-solicitud-detalle.md).

## Modelo de tarjeta

Ver [SolicitudCardModel](../04-modelo-de-datos/solicitud-card-model.md).

## Endpoints usados

| Caso | Endpoint |
|------|----------|
| Mis solicitudes (por fecha) | `GET v2/cupos/demandados/:fecha` |
| Mis demandas (rango) | `GET demanda-cupo/by-demandante?desde=X&hasta=Y` |

Ver [cupos-endpoints](../03-servicios-backend/cupos-endpoints.md).
