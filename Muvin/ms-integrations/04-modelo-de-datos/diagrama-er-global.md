# Diagrama ER Global

> **Proyecto:** `muvin-ms-integrations`
> **Base de datos:** MySQL 8.0 (`db_integrations`)
> **ORM:** Prisma 6.x
> **Revisión:** 2026-04-21

---

## Diagrama entidad-relación

```mermaid
erDiagram
    gmail_credentials {
        int id PK
        varchar_320 email "Service Account email"
        text key "Client Secret / PEM key"
        varchar project "Google Cloud Project ID"
        varchar_255 domain "Dominio de Google Workspace"
    }

    gmail_scopes {
        int id PK
        varchar_320 scope UK "URL del scope OAuth2"
        varchar_100 description "Descripción (nullable)"
        datetime createdAt
    }

    gmail_credential_scopes {
        int credential PK,FK
        int scope PK,FK
    }

    gmail_accounts {
        int id PK
        varchar_64 local UK "Parte local del email"
        datetime createdAt
        boolean watch "¿Watch activo?"
        varchar history "History ID de Gmail API (nullable)"
        datetime expiration "Expiración del watch (nullable)"
        boolean active "¿Cuenta activa para monitorear?"
    }

    gmail_labels {
        int id PK
        varchar_64 name UK "Nombre legible del label"
        varchar_64 reference UK "Label ID de Gmail API"
        enum_EType type "USER (default)"
        boolean active
        datetime createdAt
        datetime updatedAt "nullable"
    }

    gmail_messages {
        int id PK
        int label FK
        varchar_255 message "Gmail message ID"
        enum_EStatus status "PENDING | SUCCESS | ERROR | TIMEOUT"
        datetime createdAt
        datetime updatedAt "nullable"
    }

    gmail_credentials ||--o{ gmail_credential_scopes : "tiene"
    gmail_scopes ||--o{ gmail_credential_scopes : "asociado a"
    gmail_labels ||--o{ gmail_messages : "clasifica"
```

---

## Enums

### `EType`
| Valor | Descripción |
|---|---|
| `USER` | Label de tipo usuario (único valor actual) |

### `EStatus`
| Valor | Descripción |
|---|---|
| `PENDING` | Mensaje recibido, pendiente de procesamiento |
| `SUCCESS` | Mensaje procesado exitosamente |
| `ERROR` | Error en el procesamiento |
| `TIMEOUT` | Procesamiento expiró |

> [!info] Status actualizado por worker externo
> El estado `PENDING` es asignado por este microservicio al crear el registro. Los estados `SUCCESS`, `ERROR` y `TIMEOUT` son actualizados por el **worker externo** que consume la cola Bull. Este microservicio nunca los actualiza.

---

## Relaciones

| Relación | Cardinalidad | Descripción |
|---|---|---|
| `gmail_credentials` → `gmail_credential_scopes` | 1:N | Una credencial puede tener múltiples scopes |
| `gmail_scopes` → `gmail_credential_scopes` | 1:N | Un scope puede estar en múltiples credenciales |
| `gmail_labels` → `gmail_messages` | 1:N | Un label puede tener múltiples mensajes |

---

## Tablas por entidad detallada

- [[entidad-gmail-credentials]]
- [[entidad-gmail-scopes]]
- [[entidad-gmail-credential-scopes]]
- [[entidad-gmail-accounts]]
- [[entidad-gmail-labels]]
- [[entidad-gmail-messages]]

---

## Ver también

- [[modulo-core]]
- [[data-files-index]]
- [[security-inventory]]
