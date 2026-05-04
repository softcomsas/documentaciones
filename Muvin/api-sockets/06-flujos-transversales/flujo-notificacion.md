# FL-02: Notificación a Usuario por id_persona

> [[_indice-flujos]] | Módulos: [[modulo-router]]

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant BACKEND as api-panel / api
    participant REST as Express REST (:5000)
    participant R as Redis
    participant SIO as Socket.IO Server
    participant APP as Cliente Angular

    Note over APP,SIO: El cliente ya hizo configurar-usuario con id_persona=5

    BACKEND->>REST: POST /notificacion {to:[5,12], title:"Nuevo pedido", body:"...", type:"pedido"}
    REST->>R: GET listaUsuarios
    R-->>REST: [{id:"abc123",id_persona:5,name:"Carlos"}, {id:"def456",id_persona:12,name:"Ana"}]

    loop Para cada id en [5, 12]
        REST->>SIO: io.to("abc123").emit('mensaje-privado', {to:5, title:..., body:..., type:...})
        SIO-->>APP: emit('mensaje-privado', payload)
    end

    REST-->>BACKEND: { ok:true, users:[...] }
```

## Payload del evento `mensaje-privado` recibido por el cliente

```json
{
    "to": 5,
    "title": "Nuevo pedido",
    "body": "Tiene un pedido de 50 tn de soja",
    "type": "pedido"
}
```

## Escenarios de fallo

| Escenario | Resultado |
|-----------|-----------|
| Usuario no en Redis (no llamó `configurar-usuario`) | No recibe nada, sin error |
| Redis retorna `null` | `JSON.parse(null)` → excepción → HTTP 500 |
| Usuario desconectado pero aún en Redis | `io.to(id)` emite a nadie, sin error |
| Múltiples sesiones del mismo `id_persona` | Recibe el evento en todas las sesiones activas |
