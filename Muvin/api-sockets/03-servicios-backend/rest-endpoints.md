# REST Endpoints — api-sockets

> [[_indice-servicios]]

**Base URL:** `http://<host>:5000`  
**Autenticación:** Ninguna  
**Content-Type:** `application/json`

---

## GET /borrar-usuarios

Resetea la lista de usuarios en Redis.

**Respuesta:**
```json
{ "ok": true, "mensaje": "Usuarios borrados" }
```

---

## GET /mensajes

Healthcheck del servidor.

**Respuesta:**
```json
{ "ok": true, "mensaje": "Todo esta bien!!" }
```

---

## POST /mensajes

Broadcast de `mensaje-nuevo` a todos los clientes WebSocket conectados.

**Body:**
```json
{ "de": "sistema", "cuerpo": "Texto del mensaje" }
```

**Respuesta:**
```json
{ "ok": true, "cuerpo": "Texto del mensaje", "de": "sistema" }
```

---

## POST /mensajes/:id

Mensaje privado `mensaje-privado` a un socket específico por `socket.id`.

**Params:** `id` = socket.id del destinatario  
**Body:**
```json
{ "de": "operador", "title": "Aviso", "body": "Su turno está listo" }
```

**Evento emitido:**
```json
{ "to": "<socket.id>", "title": "Aviso", "body": "Su turno está listo" }
```

---

## GET /usuarios

Lista de usuarios conectados desde Redis.

**Respuesta:**
```json
[
    { "id": "abc123", "id_persona": 1, "name": "Juan" }
]
```

---

## GET /usuarios/detalle

Lista de usuarios in-memory con todos los campos.

**Respuesta:**
```json
{
    "ok": true,
    "clientes": [
        { "id": "abc123", "nombre": "Juan", "sala": "sin-sala", "id_chofer": null, "id_centro": 3, "consultas": false }
    ]
}
```

---

## POST /consulta

Emite `nueva-consulta` a todos los usuarios con `consultas === true`.

**Body:** Libre (cualquier JSON, se reenvía íntegro)

**Respuesta:**
```json
{ "ok": true, "mensaje": "Todo correcto." }
```

---

## POST /chofer-respuesta-consulta/:id_chofer

Emite `respuesta-consulta` al socket del chofer con ese `id_chofer`.

**Params:** `id_chofer` (number)  
**Body:** Libre  
**Evento emitido al chofer:** `respuesta-consulta` con req.body

**Respuesta (éxito):**
```json
{ "ok": true, "mensaje": "Existe el chofer." }
```

**Respuesta (no encontrado):**
```json
{ "ok": false, "mensaje": "No esiste el chofer." }
```

---

## GET /chofer-tienes-viaje/:id_chofer

Notifica al chofer que tiene un viaje asignado.

**Params:** `id_chofer` (number)  
**Evento emitido al chofer:** `tienes-viaje` con valor `true`

---

## POST /chofer/:id_chofer

Envía `mensaje-privado` hardcodeado al chofer. (Endpoint de uso interno/debug)

**Evento emitido:** `mensaje-privado` → `{ mensaje: 'consulta respuesta' }`

---

## POST /notificacion

Notificación personalizada a múltiples usuarios por `id_persona`.

**Body:**
```json
{
    "to": [1, 5, 23],
    "title": "Nuevo pedido",
    "body": "Tiene un pedido de 50 tn de soja",
    "type": "pedido"
}
```

**Evento emitido a cada destinatario:** `mensaje-privado`
```json
{ "to": 5, "title": "Nuevo pedido", "body": "...", "type": "pedido" }
```

**Respuesta:**
```json
{ "ok": true, "users": [ ... ] }
```
