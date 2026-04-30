# F-05 · Cupos Disponibles (Admin/Dador)

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **Ruta:** `/cupos` → `GestionPage`

## Descripción

Pantalla principal del perfil Admin/Dador. Permite seleccionar una fecha y filtros (producto, zona) para ver los cupos disponibles del día. Cada cupo puede ser asignado a un demandante desde esta pantalla.

## Campos de filtro

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Fecha | DatePicker | Fecha de los cupos (por defecto: hoy) |
| Cabecera | Dropdown | Cabecera operacional (GET `v3/cabecera/select`) |
| CCPP | Dropdown | Centro de carga (GET `v3/ccpp/:id`) |

## Acciones disponibles

- **Ver cupos:** lista paginada con disponibilidad y estado.
- **Asignar cupo:** navega a `SolicitudPage` con el cupo preseleccionado.
- **Ver mis solicitudes:** navega a `SolicitudesPage`.
- **Buscar por demandante:** navega a `BusquedaPage`.

## Endpoint principal

`GET v3/cupo/listado?fecha=YYYY-MM-DD&cabecera=X&ccpp=Y`

Ver [cupos-endpoints](../03-servicios-backend/cupos-endpoints.md).
