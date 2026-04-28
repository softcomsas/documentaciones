# Matriz de Dependencias entre Módulos

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21
>
> **Convención:** ✔ = depende de (fila → columna) | — = sin dependencia

---

## Matriz NxN

|  | AppModule | CoreModule | GmailModule | GmailService | GmailController | PrismaService | QueueService | Repos (x4) |
|---|---|---|---|---|---|---|---|---|
| **AppModule** | — | ✔ (1) | ✔ (1) | — | — | — | — | — |
| **CoreModule** | — | — | — | — | — | ✔ (2) | ✔ (2) | ✔ (2) |
| **GmailModule** | — | — | — | ✔ (2) | ✔ (2) | — | — | — |
| **GmailService** | — | — | — | — | — | — | ✔ (3) | ✔ (3) |
| **GmailController** | — | — | — | ✔ (3) | — | — | — | — |
| **PrismaService** | — | — | — | — | — | — | — | — |
| **QueueService** | — | — | — | — | — | — | — | — |
| **Repos (x4)** | — | — | — | — | — | ✔ (3) | — | — |

---

## Leyenda de tipos de dependencia

| # | Tipo | Descripción |
|---|---|---|
| (1) | Importación de módulo NestJS | `imports: [CoreModule, GmailModule]` en AppModule |
| (2) | Declaración de provider en módulo | Registrado como provider y exportado |
| (3) | Inyección de dependencia | Recibido en constructor vía DI de NestJS |

---

## Notas

- Los 4 repositories agrupados son: `GmailCredentialsRepository`, `GmailAccountsRepository`, `GmailLabelsRepository`, `GmailMessagesRepository`.
- `GmailCredentialScopesRepository` y `GmailScopesRepository` están declarados pero no aparecen en el `CoreModule` exportado (pendiente de verificar si están en el barrel `_index.ts`).
- No existen dependencias horizontales entre módulos (no hay módulo que importe a otro módulo de negocio).

---

## Ver también

- [[cross-module-dependencies]]
- [[modulo-core]]
- [[modulo-gmail]]
