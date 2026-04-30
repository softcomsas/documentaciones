# F-08 · Solicitud / Asignación de Cupo

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **Ruta:** interna → `SolicitudPage`

## Descripción

Formulario para crear una nueva solicitud de asignación de cupo. El Admin/Dador selecciona el cupo, la cantidad y el demandante. Soporta dos variantes de solicitud: simple (`carga-solicitud`) y distribuida (`carga-solicitud-distribuida`).

## Campos del formulario

| Campo | Tipo | Requerido |
|-------|------|-----------|
| Cupo seleccionado | Heredado de pantalla anterior | ✅ |
| Demandante | Selector / texto | ✅ |
| Cantidad (tn) | Numérico | ✅ |
| Tipo de solicitud | Radio (simple / distribuida) | ✅ |
| Observaciones | Texto libre | ❌ |

## Endpoints usados

| Caso | Endpoint |
|------|----------|
| Solicitud simple | `POST v3/cupo/carga-solicitud` |
| Solicitud distribuida | `POST v3/cupo/carga-solicitud-distribuida` |
| Asignación directa | `POST v3/cupo/asignar` o `v3/cupo/asignar2` |

Ver [cupos-endpoints](../03-servicios-backend/cupos-endpoints.md).
