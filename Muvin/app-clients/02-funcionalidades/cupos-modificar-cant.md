# F-11 · Modificar Cantidad de Demanda de Cupo

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **BLoC:** `ModificarCantCupoBloc`

## Descripción

Permite al usuario cambiar la cantidad solicitada de una demanda de cupo que aún está en estado pendiente. Se muestra como un dialog o pantalla modal sobre `SolicitudDetailPage`.

## Flujo

1. Usuario ingresa nueva cantidad.
2. `ModificarCantCupoBloc` valida que sea positiva y diferente a la actual.
3. Llama `POST demanda-cupo/variar-cantidad {demandaId, cantidad}`.
4. Si éxito → actualiza el stream de detalle y cierra el modal.

## Endpoint

`POST demanda-cupo/variar-cantidad`

```json
{
  "demandaId": 123,
  "cantidad": 50.5
}
```
