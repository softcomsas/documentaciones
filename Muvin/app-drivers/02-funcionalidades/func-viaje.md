# Funcionalidad: Ver Viaje Asignado

## Descripción

Cuando el chofer tiene un viaje asignado por el operador, puede ver los detalles completos desde la `HomePage`.

## Flujo

```mermaid
flowchart TD
    A[HomePage carga] --> B[FutureBuilder: obtenerMiViaje]
    B -->|Sin viaje| C[Botones: Cargas / Postulaciones habilitados]
    B -->|Con viaje y campo pdf| D[Botón Ver mi viaje habilitado]
    D --> E[Tap → ViajePagee]
    E --> F[Muestra detalle del pedido asignado]
    F --> G{¿Remito disponible?}
    G -->|Sí| H[Botón Carta de porte → CartaPortePage ROTO]
    G -->|No| I[Sólo visualización]
```

## Lógica de habilitación de botones en HomePage

```dart
// El botón "Ver mi viaje" se habilita solo si hay datos Y tiene campo 'pdf'
bool _tieneViaje = miViaje != null && miViaje['pdf'] != null;
```

## Datos mostrados en ViajePagee

| Campo | Descripción |
|-------|-------------|
| Origen | Dirección de carga |
| Destino | Dirección de entrega |
| Fecha | Fecha programada |
| Cliente | Nombre del destinatario |
| Tipo de carga | Mercadería |
| Estado | Estado actual del pedido |

## Referencias

- [[modulo-home]]
- [[modulo-viaje]]
- [[modulo-carta-porte]] — ROTO
