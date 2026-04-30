# F-06 · Cupos Disponibles (Cliente Final)

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **Ruta:** `/cuposcf`

## Descripción

Vista equivalente a F-05 pero para el perfil Cliente Final. Muestra únicamente los cupos asignados al cliente autenticado. No permite ver cupos de otros demandantes ni asignar nuevos.

## Diferencias vs Admin

| Aspecto | Admin (`/cupos`) | Cliente Final (`/cuposcf`) |
|---------|-----------------|---------------------------|
| Filtro de demandante | Libre | Fijo al userId actual |
| Puede asignar cupo | ✅ | ❌ |
| Puede buscar demandantes | ✅ | ❌ |
| Puede ver sus solicitudes | ✅ | ✅ |

## Endpoint principal

`GET v3/cupo/disponibles?userId=X&fecha=YYYY-MM-DD`

Ver [cupos-endpoints](../03-servicios-backend/cupos-endpoints.md).
