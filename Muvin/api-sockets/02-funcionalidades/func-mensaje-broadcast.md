# F-03 / F-04: Mensajes Broadcast y Privados

> [[_indice-funcionalidades]] | Módulo: [[modulo-socket]], [[modulo-router]]

## F-03 — Broadcast de mensaje

### Vía Socket.IO (cliente a todos)

**Evento escuchado:** `mensaje`  
**Payload:** `{ de: string, cuerpo: string }`  
**Emite:** `mensaje-nuevo` a **todos** los clientes

```typescript
cliente.on('mensaje', (payload) => {
    io.emit('mensaje-nuevo', payload);
});
```

### Vía REST (backend a todos)

**Endpoint:** `POST /mensajes`  
**Body:** `{ de: string, cuerpo: string }`

```bash
curl -X POST http://api-sockets:5000/mensajes \
  -H "Content-Type: application/json" \
  -d '{"de":"sistema","cuerpo":"Mantenimiento en 5 min"}'
```

**Respuesta HTTP:**
```json
{ "ok": true, "cuerpo": "Mantenimiento en 5 min", "de": "sistema" }
```

---

## F-04 — Mensaje privado por socket ID

**Endpoint:** `POST /mensajes/:id`  
**Params:** `id` = `socket.id` del destinatario  
**Body:** `{ de: string, title: string, body: string }`

**Evento emitido al destinatario:** `mensaje-privado`  
**Payload del evento:**
```json
{ "to": "<socket.id>", "title": "Hola", "body": "Mensaje de prueba" }
```

> ⚠️ Usar el `socket.id` es frágil: cambia en cada reconexión. Para mensajes por identidad de negocio, usar [[func-notificacion]] con `id_persona`.

**Respuesta HTTP:**
```json
{ "ok": true, "cuerpo": "Hola", "de": "sistema", "id": "<socket.id>" }
```
