# Diagrama ER Global

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

> [!warning] Esquema Prisma no detallado
> El archivo `prisma/schema.prisma` define el esquema de BD pero no fue relevado en profundidad (modelos, campos exactos, relaciones). El diagrama a continuación se construye a partir de los **contratos TypeScript** definidos en `src/contracts/auth/schema.ts`. Verificar contra el schema Prisma real.

---

## Entidades de ms-auth (base de datos local)

```mermaid
erDiagram
    COMPANY {
        int id PK
        string cuit
        string rs
    }

    KEY {
        int id PK
        string key
        string secret
        int company FK
        boolean active
    }

    COMPANY ||--o{ KEY : "tiene"
```

---

## Entidades de microservicios externos (referencia)

Estas entidades **no viven en la BD de ms-auth** — se acceden vía TCP a otros microservicios:

```mermaid
erDiagram
    CONTRACT {
        string reference PK
        int company FK
        int client
        int broker
        int destination
        int code
        number limit
        number volume
        number price
        date start
        date end
        string status
        string priority
    }

    LOG_LEGACY {
        id id PK
        string api
        string hash
        string user
        string method
        string endpoint
        unknown payload
        unknown response
        int code
        string status
    }
```

---

## Relación entre dominios

```mermaid
graph TD
    MS_AUTH_BD[(ms-auth\nMySQL local)]
    MS_COMM_BD[(ms-commercial\nBD remota)]
    MS_LOGS_BD[(ms-logs\nBD remota)]

    MS_AUTH_BD -->|COMPANY, KEY| AUTH_SVC[ms-auth\nservicio]
    AUTH_SVC -->|TCP: company.id| MS_COMM_BD
    AUTH_SVC -->|TCP: company.id| MS_LOGS_BD
```

---

## Notas sobre el esquema

- ⚠️ El modelo Prisma real puede tener más campos, índices y relaciones que no están reflejados aquí.
- ⚠️ `KEY.secret` debería estar hasheado en BD — sin implementación no es verificable.
- ⚠️ No se detectaron tablas de auditoría ni soft-delete en los contratos.
- ⚠️ No se detectaron migraciones versionadas en `prisma/migrations/`.
