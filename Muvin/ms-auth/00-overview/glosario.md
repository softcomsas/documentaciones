# Glosario

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## Términos de negocio

| Término | Definición |
|---------|------------|
| **Compañía** | Empresa u organización registrada en el ecosistema Muvin. Identificada por ID, CUIT y razón social. Es la entidad raíz de autenticación. |
| **CUIT** | Clave Única de Identificación Tributaria. Identificador fiscal argentino de personas jurídicas. |
| **Razón Social (rs)** | Nombre legal registrado de una empresa. |
| **Contrato comercial** | Acuerdo entre una compañía, cliente, broker y destino que define condiciones operativas (límite, volumen, precio, vigencia). Gestionado por ms-commercial. |
| **Broker** | Intermediario en una operación comercial. Referenciado por ID en los contratos. |
| **Balance de contrato** | Saldo disponible en un contrato comercial. Se modifica mediante `change-balance`. |
| **Límite de contrato** | Tope operativo del contrato. Se modifica mediante `change-limit`. |
| **Sistema Legacy** | Sistema anterior (LEGACY_PANEL y LEGACY_DESCARGAS) que sigue operativo y consume el ecosistema Muvin mediante un protocolo de autenticación heredado. |

---

## Términos técnicos

| Término | Definición |
|---------|------------|
| **Key API** | Clave pública de autenticación asignada a una compañía. Se envía en cada request. |
| **Secret** | Clave privada asociada a una Key. Usada para generar firmas — nunca viaja en requests. |
| **Firma (signature)** | Hash criptográfico generado a partir del secret, el payload y el timestamp. Permite verificar la autenticidad de un request. |
| **Timestamp** | Marca de tiempo Unix (segundos desde epoch). Usado junto con la firma para prevenir replay attacks. |
| **Replay attack** | Ataque en el que un request válido capturado previamente se reenvía para obtener acceso no autorizado. Se mitiga con ventanas de validez de timestamp. |
| **RPC (Remote Procedure Call)** | Patrón de comunicación donde el caller envía un mensaje y espera una respuesta. En NestJS usa `client.send()`. |
| **Emit (fire-and-forget)** | Patrón de comunicación donde el caller envía un evento sin esperar respuesta. En NestJS usa `client.emit()`. |
| **CMD** | Constante de string que identifica un comando RPC (ej: `'auth.validate.key'`). Definidos en `src/common/cmd/constant.ts`. |
| **Contrato tipado** | Interface TypeScript que define el payload y la respuesta de un comando RPC. Ubicados en `src/contracts/`. |
| **Handler RPC** | Función en NestJS decorada con `@MessagePattern()` o `@EventPattern()` que procesa un comando TCP. Actualmente no implementados. |
| **TContractSend** | Tipo genérico `TContractSend<Content, Response>` — define un comando RPC con respuesta esperada. |
| **TContractEmit** | Tipo genérico `TContractEmit<Content>` — define un evento fire-and-forget sin respuesta. |
| **Hash de correlación** | Identificador único generado por el caller para correlacionar el `create` y el `update` de un log. |
| **PrismaService** | Servicio NestJS que extiende PrismaClient y provee acceso a la base de datos MySQL. Provider global. |
| **CoreModule** | Módulo NestJS global que exporta PrismaService a toda la aplicación. |
| **Path alias** | Atajo de importación TypeScript (ej: `@common` → `src/common/_index`). Configurados en `tsconfig.paths.json`. |

---

## Estados y enumeraciones

| Enum / Tipo | Valores | Contexto |
|-------------|---------|----------|
| `TCommercialContractStatus` | OPEN, CLOSED, EXPIRED, VOIDED | Estado de un contrato comercial |
| `TCommercialContractPriority` | HIGHEST, HIGH, MEDIUM, LOW, LOWEST | Prioridad de un contrato comercial |
| `TLogsLegacyAPI` | LEGACY_PANEL, LEGACY_DESCARGAS | Origen de un log legacy |
| `TLogsLegacyStatus` | PENDING, SUCCESS, ERROR, TIMEOUT | Estado de un log legacy |
| `TMethod` | GET, POST, PUT, PATCH, DELETE | Verbo HTTP de un request logueado |
