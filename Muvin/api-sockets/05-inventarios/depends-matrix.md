# Matriz de Dependencias — api-sockets

> [[tree-estructura-archivos]]

## Dependencias entre módulos internos

| Módulo | Depende de | Es usado por |
|--------|------------|--------------|
| `index.ts` | `classes/server`, `routes/router`, `body-parser`, `cors` | — (entry point) |
| `classes/server.ts` | `express`, `socket.io`, `http`, `redis`, `sockets/socket`, `global/environment` | `index.ts`, `routes/router.ts` |
| `sockets/socket.ts` | `socket.io`, `classes/usuarios-lista`, `classes/usuario`, `redis`, `dotenv` | `classes/server.ts` |
| `routes/router.ts` | `express`, `classes/server`, `sockets/socket`, `redis`, `cors`, `dotenv` | `index.ts` |
| `classes/usuarios-lista.ts` | `classes/usuario` | `sockets/socket.ts`, `routes/router.ts` |
| `classes/usuario.ts` | — | `classes/usuarios-lista.ts`, `sockets/socket.ts` |
| `global/environment.ts` | `dotenv` (indirecto) | `classes/server.ts` |

## Dependencias externas

| Paquete | Versión | Usado en | Propósito |
|---------|---------|----------|-----------|
| `express` | ^4.16.4 | `index.ts`, `server.ts`, `router.ts` | Framework HTTP |
| `socket.io` | ^2.1.1 | `server.ts`, `socket.ts` | WebSocket |
| `redis` | ^4.3.0 | `server.ts`, `socket.ts`, `router.ts` | Presencia persistente |
| `cors` | ^2.8.5 | `index.ts`, `router.ts` | Headers CORS |
| `body-parser` | ^1.18.3 | `index.ts` | Parseo JSON/form |
| `dotenv` | ^16.0.1 | `socket.ts`, `router.ts` | Variables .env |

## Notas de acoplamiento

| Observación | Impacto |
|-------------|---------|
| `routes/router.ts` importa `Server.instance` directo | Alto acoplamiento REST ↔ WebSocket |
| `routes/router.ts` importa `usuariosConectados` de `socket.ts` | Acoplamiento a estado global mutable |
| Redis creado en 3 lugares (`server.ts`, `socket.ts`, `router.ts`) | 3 conexiones Redis por proceso; la de `server.ts` no se conecta |
| `socket.ts` usa `require('dotenv')` y `router.ts` también | Dotenv cargado múltiples veces (idempotente pero redundante) |
