# Flujo: Ciclo de Postulación a Carga

## Diagrama

```mermaid
flowchart TD
    A[HomePage] -->|Sin viaje activo| B[Botón Cargas habilitado]
    A -->|Con viaje activo| C[Botón Cargas deshabilitado]
    B --> D[CargasPage]
    D --> E[GET chofer-app/pedidos-publicos]
    E --> F[Swiper de tarjetas]
    F --> G[Tap tarjeta → CargasDetallePage]
    G --> H{¿Postulado?}
    H -->|No| I[Botón Postularse]
    H -->|Sí| J[Botón Cancelar]
    I --> K[POST chofer-app/postularme?id_pedido=X]
    K -->|Éxito| L[setState postulado=true]
    J --> M[POST chofer-app/des-postularme?id_pedido=X]
    M -->|Éxito| N[setState postulado=false]

    A --> O[Botón Postulaciones]
    O --> P[PostulacionesPage]
    P --> Q[GET chofer-app/postulaciones]
    Q --> R[Lista de mis postulaciones]
    R --> S[Cancelar desde lista → des-postularme]
```

## Estado final del ciclo

Cuando el operador asigna un viaje al chofer (acción del backend), la próxima vez que `HomePage` ejecute `obtenerMiViaje()` obtendrá datos y habilitará el botón "Ver mi viaje".
