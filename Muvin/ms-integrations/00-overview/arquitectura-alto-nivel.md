# Arquitectura de Alto Nivel

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Diagrama de arquitectura general

```mermaid
graph TD
    subgraph EXTERNAL["Sistemas Externos"]
        GMAIL_API["🔌 Gmail API\n(Google Cloud)"]
        GOOGLE_PUBSUB["📨 Google Pub/Sub\n(Push notifications)"]
        REDIS["📦 Redis\n(Message broker)"]
        MYSQL[("🗄️ MySQL 8.0\n(db_integrations)")]
    end

    subgraph PLATFORM["Plataforma Muvin (otros microservicios)"]
        MS_CALLER["Microservicio caller\n(envía notificaciones TCP)"]
        WORKER["Worker externo\n(consume cola Bull 'email')"]
    end

    subgraph MS_INTEGRATIONS["ms-integrations (este servicio)"]
        MAIN["main.ts\nNestFactory TCP :4006"]

        subgraph CORE["CoreModule (@Global)"]
            PRISMA["PrismaService"]
            QUEUE["QueueService"]
            REPO_CREDS["GmailCredentialsRepository"]
            REPO_ACCOUNTS["GmailAccountsRepository"]
            REPO_LABELS["GmailLabelsRepository\n(cache en memoria)"]
            REPO_MSGS["GmailMessagesRepository"]
        end

        subgraph GMAIL["GmailModule"]
            CTRL["GmailController\n@MessagePattern"]
            SVC["GmailService\nonApplicationBootstrap"]
        end
    end

    MS_CALLER -->|"TCP: integrations.email.notification"| MAIN
    MAIN --> CTRL
    CTRL --> SVC

    SVC -->|"Bootstrap: findFirstDomain()"| REPO_CREDS
    REPO_CREDS --> PRISMA --> MYSQL

    SVC -->|"Bootstrap: findAll(active)"| REPO_ACCOUNTS
    REPO_ACCOUNTS --> PRISMA

    SVC -->|"Bootstrap: gmail.users.watch()"| GMAIL_API
    GMAIL_API -.->|"Push notification\n(via Pub/Sub)"| GOOGLE_PUBSUB
    GOOGLE_PUBSUB -.->|"Reenvía evento a MS caller"| MS_CALLER

    SVC -->|"Notification: history.list()"| GMAIL_API
    SVC -->|"messages.get(format=metadata)"| GMAIL_API

    SVC -->|"match labels"| REPO_LABELS
    SVC -->|"create(labelId, messageId)"| REPO_MSGS
    REPO_MSGS --> PRISMA

    SVC -->|"queue.add(job)"| QUEUE
    QUEUE --> REDIS
    REDIS -->|"consumes email.notification"| WORKER
```

---

## Descripción de capas

### Capa de Transporte (TCP)
El servicio arranca como microservicio NestJS usando `Transport.TCP`. Escucha en `HOST:PORT` y expone `MessagePattern` para recibir comandos de otros servicios del ecosistema Muvin. No expone endpoints HTTP.

**Archivo:** `src/main.ts`, `src/config/transport.ts`

### Capa de Presentación / Mensajes
`GmailController` recibe el único mensaje activo: `integrations.email.notification`. Delega inmediatamente al servicio sin procesamiento adicional.

**Archivo:** `src/modules/gmail/controller.ts`

### Capa de Lógica de Negocio
`GmailService` contiene toda la lógica:
1. **Bootstrap:** carga credenciales, crea sesiones JWT, suscribe watches de Gmail.
2. **Procesamiento de notificaciones:** consulta historial, identifica mensajes nuevos, filtra por labels configuradas.

**Archivo:** `src/modules/gmail/service.ts`

### Capa de Infraestructura (CoreModule @Global)
Proveedores globales disponibles en toda la aplicación:
- **PrismaService:** acceso a MySQL
- **QueueService:** encolar jobs en Bull/Redis
- **Repositories:** encapsulan todas las operaciones de base de datos

**Archivos:** `src/core/`

### Capa de Datos
- **MySQL 8.0:** almacena credenciales, cuentas, labels y mensajes procesados
- **Redis:** backend de Bull para la cola `email`

### Sistemas externos
- **Gmail API (Google):** API REST para watch, history y messages
- **Google Pub/Sub:** mecanismo de entrega de notificaciones push de Gmail
- **Worker externo:** procesa los jobs de la cola (no vive en este repositorio)

---

## Flujo de vida de una notificación

```mermaid
sequenceDiagram
    participant PS as Google Pub/Sub
    participant MC as MS Caller (externo)
    participant GC as GmailController
    participant GS as GmailService
    participant GA as Gmail API
    participant DB as MySQL
    participant Q as Redis/Bull

    PS->>MC: Push notification (email recibido)
    MC->>GC: TCP msg: integrations.email.notification\n{email, history}
    GC->>GS: notification({email, history})
    GS->>GS: busca sesión en memoria por email
    GS->>GA: history.list(startHistoryId)
    GA-->>GS: {history[], historyId}
    GS->>GA: messages.get(messageId, format=metadata)
    GA-->>GS: {id, labelIds}
    GS->>GS: filtra labels contra cache en memoria
    GS->>DB: gmail_messages.create(labelId, messageId)
    DB-->>GS: {id, label, message, status: PENDING}
    GS->>Q: queue.add('email.notification', job)
    GS->>DB: gmail_accounts.update(historyId)
```

---

## Ver también

- [[vision-general]]
- [[stack-tecnologico]]
- [[modulo-core]]
- [[modulo-gmail]]
- [[flujo-notificacion-gmail]]
