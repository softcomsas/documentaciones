# FL-01: Conexión y Registro de Usuario

> [[_indice-flujos]] | Módulos: [[modulo-server]], [[modulo-socket]], [[modulo-usuarios]]

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant APP as Cliente Angular
    participant SIO as Socket.IO Server
    participant MEM as UsuariosLista (memory)
    participant R as Redis

    APP->>SIO: connect (WebSocket handshake)
    Note over SIO: socket.id asignado = "abc123"
    SIO->>MEM: agregar(new Usuario("abc123"))
    Note over MEM: Usuario {id:"abc123", nombre:"sin-nombre"}

    APP->>SIO: emit('configurar-usuario', {id_persona:5, nombre:"Carlos", consultas:false})
    SIO->>R: GET listaUsuarios
    R-->>SIO: '[{"id":"xyz","id_persona":1,"name":"Juan"}]'
    SIO->>R: SET listaUsuarios '[...anterior, {id:"abc123",id_persona:5,name:"Carlos"}]'
    SIO->>APP: emit('configurar-usuario', listaCompleta)
    SIO->>MEM: actualizar("abc123", {nombre:"Carlos", consultas:false})
    SIO-->>APP: emit('usuario-conectado', {id_persona:5, nombre:"Carlos"}) [broadcast]

    Note over APP,R: Usuario completamente registrado
```

## Estados del usuario a lo largo del flujo

| Paso | In-memory | Redis |
|------|-----------|-------|
| Antes de connect | No existe | No existe |
| Tras connect | `{nombre:'sin-nombre'}` | No existe |
| Tras configurar-usuario | `{nombre:'Carlos', consultas:false, ...}` | `{id, id_persona:5, name:'Carlos'}` |

## Casos de error

| Situación | Comportamiento |
|-----------|---------------|
| Redis no inicializado (`GET` retorna `null`) | `JSON.parse(null)` → puede causar excepción |
| Cliente desconecta antes de `configurar-usuario` | Entrada in-memory nunca se limpia |
| `configurar-usuario` enviado múltiples veces | Entradas duplicadas en Redis |
