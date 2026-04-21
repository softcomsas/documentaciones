# Arquitectura de Alto Nivel

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Ver también:** [[vision-general]], [[stack-tecnologico]], [[cross-module-dependencies]]

---

## Diagrama de arquitectura

```mermaid
graph TD
    subgraph "Plataforma Muvin (externa)"
        API["API Principal\n(muvin-ms-api)"]
        BD[("Base de Datos\n(MySQL / otro)")]
    end

    subgraph "Broker de mensajes"
        REDIS[("Redis\nBull Queue broker")]
        Q_EMAIL["Cola: email\nProceso: email.pdf"]
        Q_INT["Cola: internal\nProceso: internal.notification\n🚧 Sin procesador"]
    end

    subgraph "Muvin Worker (este servicio)"
        MAIN["main.ts\nBootstrap NestJS\n(sin servidor HTTP)"]
        APP["AppModule\nBullModule.forRoot\nBullModule.registerQueue"]
        EP["EmailProcessor\n@Processor('email')\n@Process('email.pdf')"]
        PDF["PdfParserService\nbase64 → TextResult"]
        RT["rt.ts (funciones)\nextractPartsFn\ngetAttachmentsFn\nextractAndValidateTextFn"]
    end

    subgraph "Google APIs (externo)"
        GMAIL["Gmail API v1\ngmail.users.messages.get\ngmail.users.messages.attachments.get"]
        AUTH["Google Auth\nJWT Service Account\n+ Domain-wide Delegation"]
    end

    API -->|"Publica job IJobEmailPdf\n(messageId + auth.key)"| REDIS
    REDIS --> Q_EMAIL
    Q_EMAIL -->|"Job<IJobEmailPdf>"| EP
    MAIN --> APP
    APP --> EP
    EP --> PDF
    EP --> RT
    EP -->|"JWT(email, key, scopes, subject)"| AUTH
    AUTH -->|"Token OAuth2"| GMAIL
    GMAIL -->|"Message payload"| EP
    GMAIL -->|"Attachment base64"| EP
    EP -->|"console.log(data)\n🔴 Sin persistencia"| VOID["⚠️ /dev/null\n(sin destino final)"]

    style VOID fill:#ff4444,color:#fff
    style Q_INT fill:#888,color:#fff
```

---

## Capas del sistema

### Capa 1 — Infraestructura de mensajería (Redis + Bull)

El punto de entrada real del worker no es un endpoint HTTP sino un job de Bull consumido desde Redis.

- **Broker:** Redis (host/port configurados vía env `HOST` y `PORT`)
- **Cola activa:** `email` — proceso `email.pdf`
- **Cola declarada pero inactiva:** `internal` — proceso `internal.notification`
- **Publicador de jobs:** el API principal (externo a este proyecto)

### Capa 2 — NestJS Application Layer

NestJS actúa como framework de DI y ciclo de vida, pero **no expone ningún servidor HTTP**. `NestFactory.create()` es llamado pero no se invoca `app.listen()`.

- `AppModule`: registra `BullModule` con la conexión Redis y las colas
- `EmailProcessor`: decorator `@Processor` registra el procesador en Bull

### Capa 3 — Procesamiento (lógica de negocio)

El procesador `handleMail` orquesta el flujo completo:

1. Construye cliente Gmail con credenciales JWT del job
2. Obtiene el mensaje completo desde la API de Gmail
3. Extrae partes recursivamente (`extractPartsFn`)
4. Filtra adjuntos PDF (`getAttachmentsFn`)
5. Descarga cada adjunto en base64
6. Convierte base64 → texto plano (`PdfParserService`)
7. Parsea y valida los campos del certificado (`extractAndValidateTextFn`)

### Capa 4 — Integraciones externas

- **Gmail API v1:** acceso de solo lectura a mensajes y adjuntos de cuentas corporativas de Google Workspace
- **Autenticación:** JWT de cuenta de servicio con Domain-wide Delegation (DWD)

### Capa 5 — Output (incompleto 🔴)

Actualmente el resultado del procesamiento se descarta vía `console.log()`. No hay:
- Escritura en base de datos
- Llamada de vuelta al API principal
- Publicación en otra cola
- Emisión de evento

---

## Flujo de datos simplificado

```mermaid
sequenceDiagram
    participant API as API Principal
    participant Redis as Redis/Bull
    participant Worker as EmailProcessor
    participant Gmail as Gmail API
    participant Parser as PdfParserService

    API->>Redis: addJob('email.pdf', IJobEmailPdf)
    Redis->>Worker: handleMail(job)
    Worker->>Gmail: users.messages.get(messageId)
    Gmail-->>Worker: Message + payload
    Worker->>Gmail: users.messages.attachments.get(attachmentId)
    Gmail-->>Worker: data (base64)
    Worker->>Parser: base64toText(data)
    Parser-->>Worker: TextResult
    Worker->>Worker: extractAndValidateTextFn(text)
    Note over Worker: console.log(data) 🔴 FIN SIN PERSISTENCIA
```
