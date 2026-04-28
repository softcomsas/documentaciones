---
tags: [arquitectura, diseño, microservicio]
última-revisión: 2026-04-27
---

# Arquitectura de alto nivel

> **Proyecto:** `muvin-ms-cpe` — microservicio TCP dentro del ecosistema **Muvin / BCR**

## Diagrama general del ecosistema

```mermaid
graph TD
    subgraph Ecosistema Muvin
        CPE["muvin-ms-cpe\n(ms-documentation)\nTCP :4002"]
        AUTH["MsAuth\nTCP"]
        LOGS["MsLogs\nTCP"]
        COMMERCIAL["MsCommercial\nTCP"]
        INTEGRATIONS["MsIntegrations\nTCP"]
    end

    subgraph Infraestructura
        DB[(MySQL 8.0)]
        VAULT[HashiCorp Vault\nSecretos]
        REGISTRY[Docker Registry\nprivado]
    end

    CPE -->|TCP send/emit| AUTH
    CPE -->|TCP send/emit| LOGS
    CPE -->|TCP send/emit| COMMERCIAL
    CPE -->|TCP send/emit| INTEGRATIONS
    CPE --> DB

    VAULT -->|Secretos CI/CD| REGISTRY
    REGISTRY -->|Pull imagen| CPE
```

## Diagrama de capas internas

```mermaid
graph TD
    subgraph "main.ts — Bootstrap"
        BOOT[NestFactory.createMicroservice\nTransport.TCP · HOST:PORT]
    end

    subgraph "AppModule"
        APP[AppModule\n— importa CoreModule]
    end

    subgraph "CoreModule @Global"
        PRISMA[PrismaService\nextends PrismaClient]
        REPOS[Repositories\n⚠️ Vacío]
    end

    subgraph "src/contracts — SDK de tipos"
        CAUTH[contracts/auth]
        CLOGS[contracts/logs]
        CCOMMERCIAL[contracts/commercial]
        CINTEGRATIONS[contracts/integrations]
    end

    subgraph "src/common — Utilidades"
        CMDS[cmd/constant.ts\nCMDS — message patterns]
        FNS[functions/\nLOG · successResponseFn · errResponseFn]
        IFACES[interfaces/\nTApi · IPagination · IOption]
    end

    subgraph "src/config"
        ENV[environments.ts\nValidación Joi]
        TRANS[transport.ts\nTransport.TCP]
    end

    BOOT --> APP
    APP --> CoreModule
    CoreModule --> PRISMA
    CoreModule --> REPOS
```

## Descripción de capas

| Capa | Componentes | Responsabilidad |
|------|------------|-----------------|
| **Bootstrap** | `main.ts` | Arranca el microservicio TCP. Punto de entrada único. |
| **Módulo raíz** | `AppModule` | Ensambla los módulos. Punto de extensión para futuros módulos de dominio. |
| **Core global** | `CoreModule`, `PrismaService` | Provee acceso a la base de datos globalmente. |
| **Contratos** | `src/contracts/` | Define los tipos de mensajes que este microservicio puede enviar/recibir hacia/desde otros microservicios del ecosistema. |
| **Utilidades** | `src/common/` | Helpers reutilizables: logging, respuestas estandarizadas, message patterns. |
| **Configuración** | `src/config/` | Validación de entorno y definición del protocolo de transporte. |

## Patrón de comunicación

```mermaid
sequenceDiagram
    participant Otro as Otro Microservicio
    participant CPE as muvin-ms-cpe (TCP :4002)
    participant DB as MySQL

    Otro->>CPE: ClientProxy.send(CMDS.xxx, payload)
    CPE->>DB: Prisma query
    DB-->>CPE: Resultado
    CPE-->>Otro: TApi<T> { success, data }

    Note over Otro,CPE: Comunicación TCP NestJS Microservices
    Note over CPE: Sin HTTP — sin REST — sin WebSockets
```

> [!info] Estado actual
> El microservicio tiene la infraestructura lista (TCP, Prisma, CoreModule global) pero **no tiene handlers de mensajes implementados todavía**. Al arrancar, escucha en `HOST:PORT` pero no responde a ningún `@MessagePattern`.
