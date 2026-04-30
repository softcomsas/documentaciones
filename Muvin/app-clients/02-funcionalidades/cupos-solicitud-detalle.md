# F-10 · Detalle de Solicitud de Cupo

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **Ruta:** interna → `SolicitudDetailPage`

## Descripción

Vista de detalle de una solicitud. Muestra todos los datos de la solicitud: fechas, cantidades, demandante, estado, historial de cambios. Permite modificar la cantidad si la solicitud está en estado pendiente.

## Acciones disponibles según estado

| Estado solicitud | Acciones habilitadas |
|-----------------|---------------------|
| Pendiente | Modificar cantidad (→ F-11), Recuperar cupo |
| Aprobada | Solo lectura |
| Rechazada | Solo lectura |

## Endpoints usados

| Acción | Endpoint |
|--------|----------|
| Recuperar cupo | `POST v3/cupo/recuperar` |
| Modificar cantidad | `POST demanda-cupo/variar-cantidad` |

Ver [cupos-endpoints](../03-servicios-backend/cupos-endpoints.md).
