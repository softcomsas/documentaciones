# Módulo: Server (Singleton)

> [[_indice-modulos]] | Archivo: `classes/server.ts`

## Propósito

Clase Singleton que encapsula la instancia única de:
- **Express** — servidor HTTP
- **Socket.IO** — servidor WebSocket sobre HTTP
- **Redis client** — conexión al almacenamiento de presencia

Es el núcleo de la aplicación. Todos los módulos que necesitan emitir eventos Socket.IO obtienen la instancia via `Server.instance`.

## Patrón Singleton

```typescript
public static get instance() {
    return this._intance || ( this._intance = new this() );
}
```

La instancia se crea la primera vez que se accede a `Server.instance`. Las siguientes llamadas devuelven la misma instancia.

## Propiedades públicas

| Propiedad | Tipo | Descripción |
|-----------|------|-------------|
| `app` | `express.Application` | Instancia de Express |
| `port` | `number` | Puerto leído de `SERVER_PORT` (env `PORT` ó 5000) |
| `io` | `socketIO.Server` | Instancia Socket.IO — usada para emitir eventos |
| `httpServer` | `http.Server` | Servidor HTTP subyacente |
| `clientRedis` | `any` | Cliente Redis (⚠️ typo: `clientRedist` en código) |

## Método `escucharSockets()`

Registra los 5 handlers de Socket.IO para cada nueva conexión:

```typescript
this.io.on('connection', cliente => {
    socket.conectarCliente( cliente, this.io );
    socket.configurarUsuario( cliente, this.io );
    socket.obtenerUsuarios( cliente, this.io );
    socket.mensaje( cliente, this.io );
    socket.desconectar( cliente, this.io );
});
```

## Método `start(callback)`

Inicia el servidor HTTP en `this.port`. Llamado desde `index.ts`.

## Bugs conocidos

| Bug | Descripción |
|-----|-------------|
| Typo `clientRedist` | La propiedad Redis está mal nombrada (`clientRedist` en lugar de `clientRedis`) |
| Redis sin `.connect()` | La línea `//client.connect()` está comentada — el cliente Redis de `server.ts` **no se conecta** |
| Redis duplicado | `socket.ts` crea **su propio** cliente Redis; el de `server.ts` no se usa |
