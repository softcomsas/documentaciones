# Funcionalidad: Postulación a Carga

## Descripción

El chofer puede postularse a un pedido de transporte disponible, o cancelar una postulación previa.

## Flujo — Postularse

```mermaid
flowchart TD
    A[CargasDetallePage] --> B[Tap Postularse]
    B --> C[MuvinProvider.postularse id_pedido]
    C --> D[POST chofer-app/postularme?id_pedido=X]
    D -->|Éxito| E[setState → botón cambia a Cancelar]
    D -->|Error| F[mostrarAlerta con mensaje]
```

## Flujo — Cancelar {#cancelar}

```mermaid
flowchart TD
    A[PostulacionesPage o CargasDetallePage] --> B[Tap Cancelar]
    B --> C[MuvinProvider.cancelarPostulacion id_pedido]
    C --> D[POST chofer-app/des-postularme?id_pedido=X]
    D -->|Éxito| E[setState → actualizar lista]
    D -->|Error| F[mostrarAlerta con mensaje]
```

## Restricciones

- Solo se puede postular si el chofer **no tiene un viaje activo** (controlado en `HomePage`).
- El botón de postulación se deshabilita si el campo `miViaje` tiene datos.

## Referencias

- [[modulo-cargas]]
- [[modulo-postulaciones]]
- [[modulo-muvin-provider]]
