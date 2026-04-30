# F-15 · Pedido Rápido

> **Módulo:** [modulo-cargas](../01-modulos/modulo-cargas.md)

## Descripción

Atajo para crear un pedido de transporte sin pasar por el flujo de selección de cupo. Útil cuando el cupo ya está pre-asignado externamente o cuando se opera fuera del flujo estándar.

## Endpoint

`POST pedido/rapido`

```json
{
  "patente": "ABC123",
  "chofer": "Juan Pérez",
  "dni": "12345678",
  "cantidad": 30,
  "destino": "X",
  "observaciones": ""
}
```

## Riesgos

- ⚠️ Puede crear pedidos sin validación de cupo disponible. Verificar si el backend aplica las mismas reglas de negocio que el flujo estándar.
