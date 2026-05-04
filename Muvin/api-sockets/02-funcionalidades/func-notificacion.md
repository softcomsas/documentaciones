# F-05: Notificación por id_persona

> [[_indice-funcionalidades]] | Módulo: [[modulo-router]]

## Descripción

Permite a un servicio backend enviar una notificación personalizada a uno o varios usuarios identificados por su `id_persona` (clave de negocio Muvin), sin necesidad de conocer su `socket.id`.

## Endpoint

**`POST /notificacion`**

## Payload

```json
{
    "to": [1, 5, 23],
    "title": "Nuevo pedido asignado",
    "body": "Tiene un pedido de 50 tn de soja",
    "type": "pedido"
}
```

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `to` | `number[]` | Array de `id_persona` destinatarios |
| `title` | `string` | Título de la notificación |
| `body` | `string` | Cuerpo del mensaje |
| `type` | `string` | Tipo de notificación (libre, interpretado por el cliente) |

## Algoritmo interno

```typescript
1. GET listaUsuarios desde Redis
2. Para cada id_persona en req.body.to:
   3. Para cada usuario en listaUsuarios:
      4. Si usuario.id_persona == id_persona:
         5. Emitir evento 'mensaje-privado' al socket usuario.id
            payload: { to: id_persona, title, body, type }
```

## Evento emitido al cliente

**Evento:** `mensaje-privado`
```json
{
    "to": 5,
    "title": "Nuevo pedido asignado",
    "body": "Tiene un pedido de 50 tn de soja",
    "type": "pedido"
}
```

## Respuesta HTTP

```json
{
    "ok": true,
    "users": [ { "id": "<socket.id>", "id_persona": 5, "name": "Carlos" }, ... ]
}
```

## Limitaciones

- Si el usuario no está en Redis (no llamó `configurar-usuario`), no recibe la notificación.
- Un mismo `id_persona` puede tener múltiples conexiones simultáneas — el evento se emite a **todas**.
- Si Redis contiene `null`, el `JSON.parse` lanza excepción no manejada → HTTP 500.
