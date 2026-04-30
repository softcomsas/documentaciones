# Flujo: Alta de Pedido (end-to-end)

> **Módulos involucrados:** [[modulo-pedidos]], [[modulo-shared]]

## Descripción

Flujo completo para crear una nueva solicitud de despacho (pedido/reserva). Es el flujo más crítico de la aplicación. Involucra selección de origen, destino, producto, tipo de despacho, y opcionalmente la gestión de mezclas de fertilizantes.

## Diagrama

```mermaid
flowchart TD
    A([Usuario en /pedidos]) --> B[Click en Nuevo Pedido]
    B --> C[AddPedidoComponent\nabre dialog/modal]
    C --> D[Selecciona Origen]
    D --> E{Verificar CUIT\nclient-proveedor}
    E -->|GET pedido/verify-cliente-proveedor| F{¿Válido?}
    F -->|No| G[Muestra error]
    F -->|Sí| H[AddDestinationComponent\nSelecciona Destino]
    H --> I{Validar origen\npor CUIT y destino}
    I -->|GET origen/validate-origens-by-cuit| J{¿Válido?}
    J -->|No| K[Muestra error]
    J -->|Sí| L[TipoDespachoComponent\nSelecciona modalidad]
    L --> M{¿Es fertilizante/mezcla?}
    M -->|Sí| N[ProductosMezclaComponent\nAgrega ítems de mezcla]
    N --> O[ItemMezclaComponent\npor cada ítem]
    M -->|No| P[Datos de despacho directos]
    O --> Q[Confirma pedido]
    P --> Q
    Q --> R[POST /pedido o endpoint de alta]
    R --> S{¿Éxito?}
    S -->|Sí| T[Refresca listado de pedidos]
    S -->|No| U[Muestra error]
```

## Servicios invocados

| Paso | Verbo | Ruta | Servicio |
|------|-------|------|---------|
| Verificar CUIT | GET | `pedido/verify-cliente-proveedor` | `ReservasService` |
| Validar origen-destino | GET | `origen/validate-origens-by-cuit` | `ReservasService` |
| Destinos proveedores | GET | `origen/select-merge-provider` | `ReservasService` |
| Alta de pedido | POST | ⚠️ Pendiente de verificar endpoint exacto | `PedidosService` |

## Riesgos

- ⚠️ El endpoint de alta (`POST`) del pedido no está documentado en el código analizado. 🚧 Pendiente de verificar en `add-pedido` component.
- ⚠️ Flujo multi-paso sin persistencia intermedia: si el usuario cierra el dialog, pierde todos los datos ingresados.
