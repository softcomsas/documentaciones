# Índice de Módulos — api-sockets

> [[../README]]

## Módulos del sistema

| # | Módulo | Archivo | Descripción | Criticidad |
|---|--------|---------|-------------|-----------|
| 1 | Server | `classes/server.ts` | Singleton: Express + Socket.IO + Redis | 🔴 Alta |
| 2 | Socket Handlers | `sockets/socket.ts` | Handlers de eventos WebSocket | 🔴 Alta |
| 3 | Usuarios | `classes/usuarios-lista.ts` + `usuario.ts` | Modelo y colección en memoria | 🟠 Media |
| 4 | Router REST | `routes/router.ts` | Endpoints HTTP para backends | 🔴 Alta |
| 5 | Environment | `global/environment.ts` | Variables de entorno | 🟡 Baja |

## Dependencias entre módulos

```
index.ts
 ├── classes/server.ts
 │    └── sockets/socket.ts
 │         ├── classes/usuarios-lista.ts
 │         │    └── classes/usuario.ts
 │         └── Redis (directo)
 └── routes/router.ts
      ├── classes/server.ts (Server.instance)
      ├── sockets/socket.ts (usuariosConectados)
      └── Redis (directo)
```

## Detalle

- [[modulo-server]] — Singleton Server
- [[modulo-socket]] — Handlers de eventos Socket.IO
- [[modulo-usuarios]] — Entidades Usuario y UsuariosLista
- [[modulo-router]] — Endpoints REST
