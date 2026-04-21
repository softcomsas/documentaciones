# Índice de Servicios Backend

> **Última revisión:** 2026-04-21
> **Ver también:** [[_indice-modulos]], [[arquitectura-alto-nivel]]

---

## Servicios documentados

| Servicio | Archivo | Estado |
|---------|---------|--------|
| Autenticación JWT | [[servicio-jwt]] | ✅ |
| Cola de trabajos async | [[servicio-queue]] | ✅ |
| Notificaciones (SMS, WA, push) | [[servicio-notificaciones]] | ✅ |
| Generación PDF y Excel | [[servicio-pdf-excel]] | ✅ |
| Integración AFIP | [[servicio-integracion-afip]] | ✅ |
| WebSocket / Socket.IO | [[servicio-socket]] | ✅ |

---

## Mapa de componentes

```mermaid
graph TD
    subgraph Backend Services
        JWT[JWT Auth\nfirebase/php-jwt]
        QUEUE[Queue\nyii2-queue MySQL]
        SMS[SMS\nInfobip]
        PDF[PDF\nmPDF]
        XLS[Excel\nPhpSpreadsheet]
        AFIP[AFIP\nIntegracionAfip]
        WS[WebSocket\nElephantIO]
        NOTIF[Notificaciones\nDB]
    end

    subgraph Transports
        HTTP[HTTP/cURL]
        MYSQL[(MySQL Queue)]
        SOCKET[Socket.IO]
    end

    JWT --> HTTP
    QUEUE --> MYSQL
    SMS --> HTTP
    PDF --> |render| response
    XLS --> |download| response
    AFIP --> HTTP
    WS --> SOCKET
    NOTIF --> MYSQL
```
