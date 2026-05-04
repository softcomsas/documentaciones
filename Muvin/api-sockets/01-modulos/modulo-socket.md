# Módulo: Socket Handlers

> [[_indice-modulos]] | Archivo: `sockets/socket.ts`

## Propósito

Define los handlers para cada evento Socket.IO. También crea y mantiene:
- La instancia global `usuariosConectados` (in-memory `UsuariosLista`)
- La conexión a Redis usada por todos los handlers

## Instancias globales exportadas

```typescript
export const usuariosConectados = new UsuariosLista();
// clientRedis: conectado al arrancar el módulo
```

## Handlers registrados

### `conectarCliente(cliente, io)`
**Evento:** `connection` (automático de Socket.IO)

1. Crea un `Usuario` con el `cliente.id`
2. Lo agrega a `usuariosConectados` (in-memory)
3. Log por consola

> ⚠️ **No** agrega el usuario a Redis. Solo lo agrega a in-memory. La sincronización Redis ocurre al recibir `configurar-usuario`.

---

### `configurarUsuario(cliente, io)`
**Evento escuchado:** `configurar-usuario`  
**Payload:** `{ id_persona, nombre, id_chofer?, id_centro?, consultas }`

1. Lee `listaUsuarios` de Redis
2. Hace `push` del objeto `{id: cliente.id, id_persona, name}` en el array
3. Escribe el array actualizado en Redis (`SET listaUsuarios`)
4. Emite `configurar-usuario` al cliente con la lista completa
5. Actualiza `usuariosConectados` (in-memory)
6. Broadcast `usuario-conectado` a todos los clientes

> ⚠️ **Bug de inicialización:** La condición `if(u != null || u == "[]")` siempre es true cuando Redis devuelve `null` la primera vez. El bloque `else` (que inicializa la lista) **nunca se ejecuta** en la práctica.

---

### `obtenerUsuarios(cliente, io)`
**Evento escuchado:** `obtener-usuarios`

Emite `usuarios-activos` **solo al solicitante** con la lista in-memory filtrada (usuarios con nombre ≠ `'sin-nombre'`).

---

### `mensaje(cliente, io)`
**Evento escuchado:** `mensaje`  
**Payload:** `{ de: string, cuerpo: string }`

Broadcast de `mensaje-nuevo` con el mismo payload a **todos** los clientes conectados.

---

### `desconectar(cliente, io)`
**Evento escuchado:** `disconnect`

1. Lee `listaUsuarios` de Redis
2. Busca por `cliente.id` y hace `splice` del elemento
3. Escribe el array actualizado en Redis

> ⚠️ El bloque de in-memory (`usuariosConectados.borrarUsuario`) está **comentado**. Solo se borra de Redis.
