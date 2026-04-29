# F-03 — Asignación Manual

## Descripción

Pantalla de gestión (`/assignment/management`) donde el operador vincula manualmente un **cupo disponible** con una **solicitud pendiente**, completando el proceso de asignación.

## Ruta

`/quota-operations/assignment/management`

## Flujo

```
1. Usuario selecciona cupo en GridQuotasComponent (home)
2. Navega a /assignment/management
3. Sistema muestra el cupo seleccionado + solicitudes compatibles (get-legacy-buscar-para-asignar)
4. Operador selecciona la solicitud a vincular
5. POST → post-legacy-assign-quotas o post-legacy-assign-requests
6. Sistema actualiza estado → vuelve a home con datos refrescados
```

## Queries HTTP

| Query | Descripción |
|---|---|
| `get-legacy-buscar-para-asignar` | Busca solicitudes compatibles con el cupo seleccionado |
| `get-legacy-cabecera` | Obtiene datos de cabecera del cupo |
| `post-legacy-assign-quotas` | Asigna cupo a una o más solicitudes |
| `post-legacy-assign-requests` | Asigna solicitudes a cupo |
| `post-legacy-edit-application` | Edita una aplicación de asignación ya creada |
| `post-legacy-delete-cupos` | Elimina cupos seleccionados |
| `put-legacy-cupo` | Actualiza datos de un cupo |
