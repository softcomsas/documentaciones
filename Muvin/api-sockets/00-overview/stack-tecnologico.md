# Stack Tecnológico — api-sockets

> [[vision-general]] | [[arquitectura-alto-nivel]]

## Dependencias de producción

| Paquete | Versión | Propósito |
|---------|---------|-----------|
| `express` | ^4.16.4 | Framework HTTP REST |
| `socket.io` | ^2.1.1 | Servidor WebSocket |
| `redis` | ^4.3.0 | Cliente Redis (Node Redis v4) |
| `cors` | ^2.8.5 | Middleware CORS permisivo |
| `body-parser` | ^1.18.3 | Parseo de JSON y form-data |
| `dotenv` | ^16.0.1 | Carga de variables desde `.env` |

## Dependencias de desarrollo

| Paquete | Versión | Propósito |
|---------|---------|-----------|
| `typescript` | (global) | Compilación TS → JS |
| `@types/express` | ^4.16.0 | Tipos Express |
| `@types/socket.io` | ^2.1.0 | Tipos Socket.IO |
| `@types/cors` | ^2.8.4 | Tipos CORS |

## Runtime

| Componente | Valor |
|------------|-------|
| Node.js | `current-alpine3.16` (Docker) |
| TypeScript target | ES2016 (inferido) |
| Módulos | CommonJS |
| Puerto | `5000` (default) |

## Infraestructura externa

| Servicio | Rol |
|----------|-----|
| Redis | Almacenamiento persistente de `listaUsuarios` (array JSON) |

## Notas de versiones

- **Socket.IO v2** es antigua (EOL). La versión actual es v4. La migración rompe compatibilidad con clientes v2.
- **Node Redis v4** usa API de Promises (`.connect()`, `await client.get()`), incompatible con v3.
