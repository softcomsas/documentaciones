# Eventos Socket.IO — api-sockets

> [[_indice-servicios]]

**Protocolo:** Socket.IO v2  
**URL de conexión:** `ws://<host>:5000` (upgrade desde HTTP)

---

## Eventos del cliente → servidor

### `configurar-usuario`

Registra la identidad del cliente en Redis y en memoria.

**Payload:**
```json
{
    "id_persona": 5,
    "nombre": "Carlos Pérez",
    "id_chofer": 42,
    "id_centro": 3,
    "consultas": false
}
```

**Respuestas del servidor:**
- `configurar-usuario` → al cliente: array completo de Redis
- `usuario-conectado` → broadcast: el payload original

---

### `obtener-usuarios`

Solicita la lista de usuarios activos in-memory.

**Payload:** Ninguno

**Respuesta:** `usuarios-activos` → solo al solicitante

---

### `mensaje`

Broadcast de un mensaje a todos los clientes.

**Payload:**
```json
{ "de": "Juan", "cuerpo": "Hola a todos" }
```

**Respuesta:** `mensaje-nuevo` → a todos los clientes

---

## Eventos servidor → cliente

| Evento | Trigger | Payload | Destinatario |
|--------|---------|---------|--------------|
| `configurar-usuario` | Tras `configurar-usuario` | `[{id, id_persona, name}]` | Solo al cliente |
| `usuario-conectado` | Tras `configurar-usuario` | `{id_persona, nombre, ...}` | Todos |
| `usuarios-activos` | Tras `obtener-usuarios` | `Usuario[]` | Solo al solicitante |
| `mensaje-nuevo` | Tras `mensaje` o REST POST /mensajes | `{de, cuerpo}` | Todos |
| `mensaje-privado` | REST POST /mensajes/:id, /chofer/:id, /notificacion | `{to, title, body, type?}` | Socket específico |
| `nueva-consulta` | REST POST /consulta | req.body | Usuarios con `consultas=true` |
| `respuesta-consulta` | REST POST /chofer-respuesta-consulta/:id | req.body | Chofer específico |
| `tienes-viaje` | REST GET /chofer-tienes-viaje/:id | `true` | Chofer específico |

---

## Ejemplo de integración Angular

```typescript
// Conexión
const socket = io('http://api-sockets:5000');

// Registrar usuario
socket.emit('configurar-usuario', {
    id_persona: 5,
    nombre: 'Carlos',
    consultas: false
});

// Escuchar notificaciones
socket.on('mensaje-privado', (data) => {
    console.log('Notificación recibida:', data);
});

// Escuchar nuevo usuario
socket.on('usuario-conectado', (user) => {
    console.log('Nuevo usuario:', user);
});
```
