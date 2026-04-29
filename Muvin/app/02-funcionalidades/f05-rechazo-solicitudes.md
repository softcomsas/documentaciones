# F-05 — Rechazo de Solicitudes

## Descripción

Funcionalidad dentro de management que permite al operador **rechazar solicitudes** seleccionadas, indicando un motivo de rechazo.

## Flujo

```
1. Operador selecciona una o más solicitudes en GridRequestsComponent
2. Hace clic en "Rechazar"
3. Sistema muestra selector de motivo (get-legacy-motivos-rechazo)
4. Operador confirma motivo
5. POST → post-legacy-rechazar-solicitudes
6. AssignmentService refresca datos
```

## Queries HTTP

| Query | Descripción |
|---|---|
| `get-legacy-motivos-rechazo` | Lista de motivos de rechazo disponibles |
| `post-legacy-rechazar-solicitudes` | Registra el rechazo con el motivo seleccionado |

## Motivos de rechazo

Catálogo cargado desde el backend legado. Se obtiene al inicializar `AssignmentService` como parte del `forkJoin` de carga inicial.
