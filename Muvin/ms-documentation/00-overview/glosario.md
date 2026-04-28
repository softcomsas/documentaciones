---
tags: [glosario, términos, dominio]
última-revisión: 2026-04-27
---

# Glosario

## Términos técnicos del proyecto

| Término | Definición |
|---------|-----------|
| **CPE** | Nombre interno del microservicio (`muvin-ms-cpe`). Sigla del dominio funcional. 🚧 Pendiente de verificar significado exacto. |
| **Microservicio TCP** | Servicio NestJS que se comunica exclusivamente vía protocolo TCP usando el sistema de mensajería de NestJS Microservices. No expone HTTP. |
| **`send()`** | Método de `ClientProxy` para enviar un mensaje y esperar respuesta (request/response). Equivalente a una llamada RPC. |
| **`emit()`** | Método de `ClientProxy` para enviar un mensaje sin esperar respuesta (fire and forget). Usado para eventos y notificaciones. |
| **`@MessagePattern`** | Decorador NestJS que registra un handler para un patrón de mensaje TCP específico. |
| **CMDS** | Objeto centralizado en `src/common/cmd/constant.ts` con todos los strings de message patterns del ecosistema. |
| **`TApi<T>`** | Tipo union discriminado que representa una respuesta estándar del sistema: `{ success: true, data: T }` o `{ success: false, error: {...} }`. |
| **`TContractSend<C, R>`** | Tipo que describe un contrato request/response entre microservicios: entrada `C`, respuesta `TApi<R>`. |
| **`TContractEmit<C>`** | Tipo que describe un contrato fire-and-forget: solo entrada `C`, sin respuesta. |
| **CoreModule** | Módulo NestJS global (`@Global()`) que provee `PrismaService` a toda la aplicación. |
| **Path alias** | Atajos de importación TypeScript (`@common`, `@config`, `@core`, `@contracts`, `@db`) definidos en `tsconfig.paths.json`. |
| **Prisma** | ORM utilizado para acceder a MySQL. El cliente se genera con `prisma generate` a partir del esquema en `prisma/schema.prisma`. |
| **muvin-net** | Red Docker interna del ecosistema Muvin. Todos los contenedores se comunican dentro de esta red. |

## Microservicios del ecosistema

| Nombre | Sigla | Responsabilidad |
|--------|-------|-----------------|
| `muvin-ms-cpe` | CPE | Este microservicio (funcionalidad pendiente de definir) |
| `MsAuth` | Auth | Autenticación, empresas, validación de claves y firmas |
| `MsLogs` | Logs | Registro de auditoría de llamadas a sistemas legacy |
| `MsCommercial` | Commercial | Gestión de contratos comerciales |
| `MsIntegrations` | Integrations | Integraciones externas (email, etc.) |

## Tipos de dominio

| Tipo | Valores | Descripción |
|------|---------|-------------|
| `TMethod` | `GET`, `POST`, `PUT`, `PATCH`, `DELETE` | Métodos HTTP registrados en logs |
| `TLogsLegacyStatus` | `PENDING`, `SUCCESS`, `ERROR`, `TIMEOUT` | Estado de una llamada a sistema legacy |
| `TLogsLegacyAPI` | `LEGACY_PANEL`, `LEGACY_DESCARGAS` | APIs legacy del sistema BCR |
| `TCommercialContractStatus` | `OPEN`, `CLOSED`, `EXPIRED`, `VOIDED` | Estado de un contrato comercial |
| `TCommercialContractPriority` | `HIGHEST`, `HIGH`, `MEDIUM`, `LOW`, `LOWEST` | Prioridad de un contrato comercial |
