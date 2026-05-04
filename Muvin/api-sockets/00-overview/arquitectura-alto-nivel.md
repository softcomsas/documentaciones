# Arquitectura de Alto Nivel — api-sockets

> [[vision-general]] | [[stack-tecnologico]]

## Diagrama de componentes

```mermaid
graph TD
    subgraph "Clientes WebSocket"
        APP[app Angular]
        AGRO[app-agronomy Angular]
        PANEL[app-panel Angular]
    end

    subgraph "Emisores REST"
        API_PANEL[api-panel PHP/Yii2]
        API[api NestJS]
    end

    subgraph "api-sockets (Node.js :5000)"
        EXPRESS[Express HTTP Server]
        SIOSERVER[Socket.IO Server v2]
        ROUTER[router.ts\nREST endpoints]
        SOCKET_HANDLERS[sockets/socket.ts\nEvent handlers]
        SERVER_CLASS[classes/server.ts\nSingleton Server]
        USUARIOS_LISTA[classes/usuarios-lista.ts\nIn-memory list]
        USUARIO_CLASS[classes/usuario.ts\nUser model]
    end

    subgraph "Infraestructura"
        REDIS[(Redis\nlistaUsuarios)]
    end

    APP -- "WebSocket" --> SIOSERVER
    AGRO -- "WebSocket" --> SIOSERVER
    PANEL -- "WebSocket" --> SIOSERVER

    API_PANEL -- "REST HTTP" --> ROUTER
    API -- "REST HTTP" --> ROUTER

    SERVER_CLASS --> EXPRESS
    SERVER_CLASS --> SIOSERVER
    SERVER_CLASS --> SOCKET_HANDLERS

    ROUTER --> REDIS
    ROUTER --> SIOSERVER
    SOCKET_HANDLERS --> REDIS
    SOCKET_HANDLERS --> USUARIOS_LISTA
    USUARIOS_LISTA --> USUARIO_CLASS
```

## Flujo de datos simplificado

```
Cliente Angular                api-sockets                    Redis
     │                              │                           │
     │── connect ──────────────────►│                           │
     │                              │── GET listaUsuarios ─────►│
     │── configurar-usuario ────────►│                           │
     │                              │── SET listaUsuarios ─────►│
     │                              │── emit usuario-conectado ─►│(todos)
     │                              │                           │
     │                              │                           │
[api-panel REST POST /notificacion] │                           │
     │                              │── GET listaUsuarios ─────►│
     │                              │◄─ lista ─────────────────│
     │◄─ emit mensaje-privado ──────│                           │
```

## Separación de responsabilidades

| Capa | Archivo | Responsabilidad |
|------|---------|-----------------|
| Bootstrap | `index.ts` | Arranque, middlewares, rutas |
| Singleton | `classes/server.ts` | Instancia única Express+Socket.IO+Redis |
| Eventos WS | `sockets/socket.ts` | Handlers de eventos Socket.IO |
| REST | `routes/router.ts` | Endpoints HTTP para emisión desde backends |
| Dominio | `classes/usuario.ts` / `usuarios-lista.ts` | Modelo y colección de usuarios |
| Config | `global/environment.ts` | Variables de entorno |
