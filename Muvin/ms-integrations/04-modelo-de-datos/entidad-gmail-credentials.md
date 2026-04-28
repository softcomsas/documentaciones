# Entidad: gmail_credentials

> **Tabla:** `gmail_credentials`
> **Módulo Prisma:** `gmail_credentials`
> **Revisión:** 2026-04-21

## Descripción

Almacena las credenciales de service account de Google Cloud utilizadas para autenticar requests a la Gmail API mediante JWT con Domain-Wide Delegation. Una fila representa un proyecto de Google Cloud completo con su clave privada y dominio de Workspace.

> [!danger] Datos altamente sensibles
> Esta tabla contiene **claves privadas** de service account. Requiere restricción de acceso estricta a nivel de base de datos. Ver [[security-inventory]].

## Campos

| Campo | Tipo | Nulable | Descripción |
|---|---|---|---|
| `id` | `INT` PK AI | No | Identificador autoincremental |
| `email` | `VARCHAR(320)` | No | Email del service account de Google |
| `key` | `TEXT` | No | Clave privada PEM (almacenada con `\n` literal — requiere normalización) |
| `project` | `VARCHAR` | No | Project ID de Google Cloud (también usado como `topicName` en Pub/Sub) |
| `domain` | `VARCHAR(255)` | No | Dominio de Google Workspace que administra |

## Relaciones

| Relación | Tipo | Descripción |
|---|---|---|
| `scopes` | 1:N via `gmail_credential_scopes` | Scopes OAuth2 habilitados para esta credencial |

## Índices

| Índice | Campos | Tipo |
|---|---|---|
| PRIMARY | `id` | PK |

## Módulos que la consumen

- [[modulo-core]] → `GmailCredentialsRepository.findFirstDomain()`
- [[modulo-gmail]] → `GmailService._setToken()`

## Riesgos

- 🔴 La clave privada se almacena en texto plano en la base de datos. No hay cifrado at-rest documentado.
- ⚠️ Solo se usa `findFirst()` sin criterio — no soporta múltiples credenciales/dominios.
- ⚠️ La clave contiene `\n` literal y requiere `replace(/\\n/g, '\n')` antes de usarla. Esto es frágil.

## Archivo fuente

- `prisma/schema.prisma` — modelo `gmail_credentials`
- `src/core/repositories/gmail-credentials.ts`
