# Flujo: GPS Tracking Continuo

## Diagrama

```mermaid
flowchart TD
    A[HomePage.initState] --> B[Timer.periodic 10 segundos]
    B --> C[GeolocatorProvider.getCurrentLocation]
    C --> D{GPS disponible?}
    D -->|No| E[No envía — siguiente tick]
    D -->|Sí| F[actualizarPosicion lat lng]
    F --> G[POST chofer-app/actualizar-posicion]
    G -->|Respuesta| H[Sin acción — fuego y olvido]
    H --> B

    B -.->|BUG: nunca se cancela| I[Widget destruido]
    I -.->|Timer sigue corriendo| B
```

## Ciclo de vida del Timer

| Evento | Comportamiento correcto | Comportamiento actual |
|--------|------------------------|----------------------|
| `initState` | Crear timer | ✅ Crea timer |
| Navegar fuera de Home | `dispose()` cancela timer | 🔴 **Timer NO se cancela** |
| Widget destruido | Timer detenido | 🔴 **Timer sigue activo** |
| App en background | Timer pausado | 🔴 **Puede seguir activo** |

## Impacto del bug

- Requests HTTP continuos al backend sin propósito.
- Batería del dispositivo drenada innecesariamente.
- GPS activo aunque el chofer no esté en la pantalla principal.
- Potencial acumulación de Timers si se navega múltiples veces a/desde Home.
