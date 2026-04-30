# F-07 · Búsqueda de Cupos por Demandante

> **Módulo:** [modulo-cupos](../01-modulos/modulo-cupos.md)
> **Ruta:** interna dentro de `/cupos` → `BusquedaPage`

## Descripción

Permite al perfil Admin buscar cupos filtrados por demandante (nombre o CUIT). Usa `BusquedaBloc` con debounce RxDart para no disparar requests en cada keystroke.

## Comportamiento

1. Usuario escribe en el campo de búsqueda.
2. `BusquedaBloc` aplica `debounceTime(400ms)` antes de llamar al backend.
3. Se llama `GET v2/cupos/buscar-x-demandante?q=texto`.
4. Resultados se muestran en lista. Tap en un resultado → `SolicitudPage`.

## Riesgos

- 💀 Usa endpoint v2 (`v2/cupos/buscar-x-demandante`). Verificar si fue migrado a v3.
