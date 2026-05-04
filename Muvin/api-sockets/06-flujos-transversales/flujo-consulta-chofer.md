# FL-03: Consulta y Respuesta de Chofer

> [[_indice-flujos]] | Módulos: [[modulo-router]], [[modulo-usuarios]]

## Contexto

Un chofer (en app-agronomy) solicita información. Un operador logístico ("consultor") la recibe y responde. El ciclo completo pasa por api-sockets.

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant CHOFER as App Chofer (Socket id_chofer=42)
    participant CONSULTOR as App Consultor (consultas=true)
    participant SIO as Socket.IO Server
    participant MEM as UsuariosLista (memory)
    participant APIPANEL as api-panel

    Note over CHOFER,SIO: Ambos ya hicieron configurar-usuario

    APIPANEL->>SIO: POST /consulta {id_chofer:42, pregunta:"¿Cuál es el turno para maíz?"}
    SIO->>MEM: getConsultas() → usuarios con consultas=true
    MEM-->>SIO: [{id:"cons123", ...}]
    SIO-->>CONSULTOR: emit('nueva-consulta', {id_chofer:42, pregunta:"..."})

    Note over CONSULTOR: Operador ve la consulta y responde

    APIPANEL->>SIO: POST /chofer-respuesta-consulta/42 {respuesta:"Turno 3, gate B"}
    SIO->>MEM: getChofer(42) → {id:"chofer456"}
    MEM-->>SIO: {id:"chofer456", ...}
    SIO-->>CHOFER: emit('respuesta-consulta', {respuesta:"Turno 3, gate B"})

    Note over APIPANEL,CHOFER: Si hay viaje asignado:
    APIPANEL->>SIO: GET /chofer-tienes-viaje/42
    SIO->>MEM: getChofer(42)
    SIO-->>CHOFER: emit('tienes-viaje', true)
```

## Requisito previo crítico

`getChofer(id_chofer)` y `getConsultas()` buscan en **in-memory** (`UsuariosLista`).  
Si el proceso Node reinició, la lista está vacía y ningún chofer ni consultor será encontrado.

**Solución operativa:** Tras reiniciar el servidor, todos los clientes deben reconectarse y re-emitir `configurar-usuario`.
