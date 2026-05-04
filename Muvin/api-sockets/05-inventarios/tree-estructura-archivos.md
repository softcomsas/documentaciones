# Árbol de Estructura de Archivos — api-sockets

> [[../README]]

**Raíz:** `api-sockets-main/api-sockets/`

```
api-sockets/
├── package.json              # socket-server, Node.js, dependencias
├── tsconfig.json             # Configuración TypeScript
├── package-lock.json         # Lock de dependencias npm
├── Dockerfile                # Imagen Docker: node:current-alpine3.16
├── docker-compose.yml.example # Ejemplo de orquestación Docker
├── .dockerignore
├── .gitlab-ci.yml            # CI/CD GitLab
│
├── index.ts                  # ⚙️ Entry point: bootstrap Express + Server
│
├── global/
│   └── environment.ts        # SERVER_PORT = process.env.PORT || 5000
│
├── classes/
│   ├── server.ts             # 🔴 Singleton Server (Express + Socket.IO + Redis)
│   ├── usuario.ts            # Entidad de presencia Usuario
│   └── usuarios-lista.ts     # Colección in-memory de usuarios
│
├── sockets/
│   └── socket.ts             # 🔴 Handlers de eventos Socket.IO + Redis client
│
├── routes/
│   └── router.ts             # 🔴 11 endpoints REST Express
│
└── dist/                     # [generado] Compilación TypeScript → JavaScript
    ├── index.js
    ├── global/
    │   └── environment.js
    ├── classes/
    │   └── server.js
    ├── sockets/
    │   └── socket.js
    └── routes/
        └── router.js
```

## Archivos clave

| Archivo | Rol | Criticidad |
|---------|-----|-----------|
| `index.ts` | Bootstrap, middlewares, arranque | 🔴 |
| `classes/server.ts` | Singleton central | 🔴 |
| `sockets/socket.ts` | Toda la lógica WebSocket + Redis | 🔴 |
| `routes/router.ts` | REST API para backends | 🔴 |
| `classes/usuarios-lista.ts` | Colección in-memory | 🟠 |
| `classes/usuario.ts` | Modelo de datos | 🟡 |
| `global/environment.ts` | Config de puerto | 🟡 |

## Métricas

| Métrica | Valor |
|---------|-------|
| Archivos TypeScript | 7 |
| Líneas de código (aprox.) | ~550 |
| Endpoints REST | 11 |
| Eventos Socket.IO (entrada) | 4 |
| Eventos Socket.IO (salida) | 8 |
| Dependencias producción | 6 |
