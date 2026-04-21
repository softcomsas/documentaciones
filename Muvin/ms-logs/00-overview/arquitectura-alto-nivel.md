# Arquitectura de Alto Nivel — `muvin-ms-logs`

> **Última revisión:** 2026-04-21
> **Tipo de diagrama:** `graph TD` (capas)

---

## Descripción del sistema

`muvin-ms-logs` es un **microservicio de auditoría y logging** dentro del ecosistema BCR-Muvin. No expone endpoints HTTP ni interfaz de usuario. Opera exclusivamente como **receptor TCP de mensajes** provenientes de otros servicios del ecosistema.

Su responsabilidad es registrar dos tipos de eventos:

1. **Trazas GraphQL** (`traces` + `events`): el gateway GraphQL registra cada operación y cada llamada a microservicios como un par traza-evento correlacionados por un `hash`.
2. **Requests legacy** (`legacy_panel` + `legacy_descargas`): los sistemas legados registran cada request HTTP a sus APIs para auditoría, con búsqueda posterior por ID, usuario o términos de negocio.

---

## Diagrama de arquitectura — capas

```mermaid
graph TD
    subgraph "Ecosistema BCR-Muvin (externos a este MS)"
        GW["🌐 Gateway GraphQL<br/>(ms-gateway)"]
        MS_OTHER["🔌 Otros Microservicios<br/>(ms-legacy, ms-xxx)"]
        LEGACY_API["📦 Sistema LEGACY_PANEL<br/>Sistema LEGACY_DESCARGAS"]
    end

    subgraph "muvin-ms-logs — Capa de Transporte"
        TCP_LISTEN["🔌 TCP Listener<br/>host:port (env)"]
    end

    subgraph "muvin-ms-logs — Capa de Aplicación"
        MS_CTRL["MicroservicesController<br/>trace.create · trace.update<br/>event.create · ⚠️event.update"]
        LEG_CTRL["LegacyController<br/>legacy.create · legacy.update<br/>search.id · search.user · search.terms"]
    end

    subgraph "muvin-ms-logs — Capa de Negocio"
        MS_SVC["MicroservicesService<br/>createTrace · updateTrace<br/>createEvent · updateEvent"]
        LEG_SVC["LegacyService<br/>create · update<br/>searchId · searchUser · searchTerms"]
        BROTLI["🗜️ Brotli Compression<br/>compressJsonFn · decompressJsonFn"]
        TERMS["🔍 Term Extractor<br/>extractSearchableFieldsFn<br/>mergeSearchTermsFn"]
    end

    subgraph "muvin-ms-logs — Capa de Datos"
        PRISMA["Prisma ORM v6<br/>PrismaService"]
    end

    subgraph "Persistencia"
        MYSQL[("🗄️ MySQL 8.0<br/>db_logs<br/>7 tablas")]
    end

    GW -->|"TCP: logs.trace.create/update"| TCP_LISTEN
    MS_OTHER -->|"TCP: logs.event.create/update"| TCP_LISTEN
    LEGACY_API -->|"TCP: logs.legacy.*"| TCP_LISTEN

    TCP_LISTEN --> MS_CTRL
    TCP_LISTEN --> LEG_CTRL

    MS_CTRL --> MS_SVC
    LEG_CTRL --> LEG_SVC

    MS_SVC --> PRISMA
    LEG_SVC --> BROTLI
    LEG_SVC --> TERMS
    LEG_SVC --> PRISMA

    PRISMA --> MYSQL
```

---

## Descripción de cada capa

### 1. Capa de Transporte (TCP)
- El MS arranca como un **NestJS Microservice** (no como aplicación HTTP).
- Escucha en `LOGS_MICROSERVICE_HOST:LOGS_MICROSERVICE_PORT` usando el transporte configurado en `LOGS_MICROSERVICE_TRANSPORT`.
- Todos los mensajes entrantes son **fire-and-forget** para operaciones de escritura (create/update): el caller no espera respuesta.
- Las operaciones de **búsqueda** (search.*) sí devuelven respuesta.

### 2. Capa de Aplicación (Controllers)
- `MicroservicesController`: maneja 4 message patterns para traces y events.
- `LegacyController`: maneja 5 message patterns para el sistema legado.
- Los controllers **no hacen validación de negocio** — delegan inmediatamente al service.
- Usan `ValidationPipe` global con `whitelist: true` y `forbidNonWhitelisted: true`.

### 3. Capa de Negocio (Services)
- `MicroservicesService`: CRUD directo sobre tablas `traces` y `events`. Sin lógica especial.
- `LegacyService`: lógica más compleja — upsert de acciones, compresión de payloads, extracción de términos de búsqueda, correlación por `hash`.
- Los errores en operaciones de escritura se **capturan silenciosamente** con `console.log` (no se propagan). Ver [[deuda-tecnica]].

### 4. Capa de Datos (Prisma + MySQL)
- `PrismaService` extiende `PrismaClient` e implementa `OnModuleInit`.
- **Problema:** cada módulo (`MicroservicesModule`, `LegacyModule`) instancia su propio `PrismaService` como provider — hay dos instancias del cliente Prisma. Ver [[deuda-tecnica]].

---

## Flujo de correlación traza-evento

```mermaid
sequenceDiagram
    participant GW as Gateway GraphQL
    participant LOG as ms-logs

    GW->>LOG: logs.trace.create {hash, operation, payload}
    Note over LOG: Inserta trace con status=PENDING

    GW->>MS: Llama a microservicio
    MS->>LOG: logs.event.create {trace=hash, hash=eventHash, service, payload}
    Note over LOG: Inserta event vinculado al trace

    MS-->>GW: Respuesta del MS
    GW->>LOG: logs.event.⚠️create {hash=eventHash, response}
    Note over LOG: ⚠️ BUG: debería ser logs.event.update — nunca se ejecuta

    GW->>LOG: logs.trace.update {hash, response, status}
    Note over LOG: Actualiza trace con duration y status final
```

> ⚠️ El paso de `event.update` nunca se ejecuta correctamente debido al bug en `constant.ts`. Ver [[deuda-tecnica#bug-event-update]].

---

## Posición en el ecosistema

```mermaid
graph LR
    subgraph "BCR-Muvin (inferido)"
        GW["ms-gateway<br/>🌐 GraphQL API"]
        MS_LEGACY["ms-legacy<br/>📦 API REST Legada"]
        MS_LOGS["ms-logs<br/>📋 Auditoría"]
        CLIENTS["🖥️ Clientes<br/>(navegadores, apps)"]
    end

    CLIENTS --> GW
    GW --> MS_LEGACY
    GW --> MS_LOGS
    MS_LEGACY --> MS_LOGS
```

> ⚠️ Los servicios externos (`ms-gateway`, `ms-legacy`) son **inferidos** del código — no están disponibles en este workspace. Los nombres y relaciones son aproximaciones. Marcar como 🚧 hasta verificación.

---

*Ver también: [[stack-tecnologico]] · [[cross-module-dependencies]] · [[flujo-tracing-graphql]] · [[flujo-legacy-logging]]*
