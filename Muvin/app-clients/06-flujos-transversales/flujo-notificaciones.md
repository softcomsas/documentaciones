# Flujo: Notificaciones y Tiempo Real

> [[_indice-flujos]] | Módulo: [[modulo-home]] | Funcionalidades: [[home-notificaciones]] · [[home-socket]]

## Descripción

Dos canales paralelos de comunicación servidor → cliente: Firebase Cloud Messaging (push) para notificaciones y Socket.IO para actualizaciones en tiempo real.

## Inicialización al abrir HomePage

```mermaid
sequenceDiagram
    participant HomePage
    participant Firebase
    participant SocketIO
    participant Backend
    participant Preference

    HomePage->>Firebase: getToken() → fcmToken
    Firebase-->>HomePage: "fXq..."
    HomePage->>Preference: saveFcmToken(fcmToken)
    Note over HomePage: Opcional: enviar token al backend

    HomePage->>SocketIO: io('socket.muvinapp.com')
    SocketIO->>Backend: WebSocket handshake
    Backend-->>SocketIO: connected
    SocketIO-->>HomePage: 'connect' event

    HomePage->>Firebase: onMessage.listen()
    HomePage->>SocketIO: on('cupo:update', handler)
    HomePage->>SocketIO: on('carga:update', handler)
```

## Flujo de notificación push (FCM)

```mermaid
sequenceDiagram
    participant Backend
    participant FCMServer as Firebase FCM
    participant App
    participant BLoC

    Backend->>FCMServer: POST (serverToken + fcmToken + payload)
    FCMServer->>App: Push notification

    alt App en primer plano
        App->>BLoC: onMessage → reload data
        App-->>Usuario: Snackbar informativo
    else App en segundo plano
        App-->>Usuario: Notificación del sistema operativo
        Usuario->>App: tap notificación → abre app
        App->>BLoC: onMessageOpenedApp → navegar a sección
    end
```

## Flujo de actualización por Socket.IO

```mermaid
sequenceDiagram
    participant Backend
    participant SocketServer as Socket.IO Server
    participant App
    participant BLoC

    Backend->>SocketServer: emit('cupo:update', data)
    SocketServer->>App: evento 'cupo:update'
    App->>BLoC: recargar stream de cupos
    BLoC-->>UI: StreamBuilder reconstruye widget
```

## Riesgos

| Riesgo | Severidad |
|--------|-----------|
| Server token FCM hardcodeado → envío no autorizado de push | 🔴 Crítico |
| Sin reconexión automática del socket | ⚠️ Medio |
| FCM library ^6.x incompatible con Flutter 3.x | 🔴 Bloquea upgrade |
