# Índice: Modelo de Datos

> **Contexto:** [[arquitectura-alto-nivel]] · [[_indice-servicios]]

## Resumen

`ms-logs` usa MySQL 8.0 a través de Prisma ORM con **7 modelos** (tablas) y **4 enums**.

## Diagrama ER global

```mermaid
erDiagram
    users {
        UInt id PK
        VarChar20 name
    }
    legacy_actions {
        Int id PK
        EProject api
        VarChar200 endpoint
        EMethods method
    }
    legacy_user_actions {
        Int id PK
        Int user FK
        Int action FK
    }
    legacy_panel {
        Int id PK
        VarChar50 hash UK
        Int user FK
        Int action FK
        VarChar search_terms
        Bytes payload
        Bytes response
        Int code
        EStatus status
        DateTime createdAt
        DateTime finishedAt
        Int duration
    }
    legacy_descargas {
        Int id PK
        VarChar50 hash UK
        Int user FK
        Int action FK
        VarChar search_terms
        Bytes payload
        Bytes response
        Int code
        EStatus status
        DateTime createdAt
        DateTime finishedAt
        Int duration
    }
    traces {
        Int id PK
        VarChar50 hash UK
        Int user
        VarChar200 operation
        EOperations type
        Json payload
        Json response
        EStatus status
        Int duration
        DateTime createdAt
        DateTime finishedAt
    }
    events {
        Int id PK
        VarChar50 trace
        VarChar50 hash UK
        VarChar30 service
        Int duration
        DateTime createdAt
        DateTime finishedAt
        Json payload
        Json response
    }

    users ||--o{ legacy_user_actions : "tiene"
    legacy_actions ||--o{ legacy_user_actions : "referenciada por"
    users ||--o{ legacy_panel : "registra"
    legacy_actions ||--o{ legacy_panel : "acción"
    users ||--o{ legacy_descargas : "registra"
    legacy_actions ||--o{ legacy_descargas : "acción"
```

> ℹ️ `events.trace` es un `VARCHAR(50)` que referencia `traces.hash` — **sin FK declarada en BD**. La integridad referencial es responsabilidad de la aplicación.

## Entidades detalladas

| Entidad | Archivo | Descripción |
|---------|---------|-------------|
| [[entidad-traces]] | `prisma/schema.prisma` | Trazas de operaciones GraphQL |
| [[entidad-events]] | `prisma/schema.prisma` | Eventos de llamadas a microservicios |
| [[entidad-users]] | `prisma/schema.prisma` | Mirror de usuarios del sistema |
| [[entidad-legacy]] | `prisma/schema.prisma` | Logs de requests a sistemas legados (panel + descargas) |

---

*Ver también: [[diagrama-er-global]] · [[arquitectura-alto-nivel]]*
