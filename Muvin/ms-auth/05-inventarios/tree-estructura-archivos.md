# Inventario: Árbol de Estructura de Archivos

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27
> **Generado desde:** raíz del repositorio

---

## Árbol de directorios

```
ms-auth/
├── .github/                          # CI/CD — GitHub Actions
│   └── workflows/
│       ├── deploy-dev.yml            # 🔄 Pipeline de deploy a entorno DEV
│       └── sync-cap.yml              # 🔄 Sincronización con CAP (⚠️ Pendiente de verificar propósito)
│
├── .husky/                           # Git hooks
│   └── pre-commit                    # Ejecuta lint-staged antes de cada commit
│
├── docker/                           # Infraestructura de contenedores
│   ├── Dockerfile                    # Multi-stage build: builder + producción (node:20-alpine)
│   └── docker-compose.yml            # Levanta MySQL 8.0 + microservicio
│
├── prisma/                           # ORM y esquema de base de datos
│   ├── schema.prisma                 # Definición del esquema (provider: MySQL)
│   └── generated/                    # ⚠️ Generado automáticamente — NO editar manualmente
│       └── client/                   # Cliente Prisma generado por `prisma generate`
│
├── src/                              # Código fuente principal
│   │
│   ├── main.ts                       # Punto de entrada — bootstrap del microservicio TCP
│   ├── module.ts                     # AppModule raíz — importa CoreModule
│   │
│   ├── common/                       # Utilidades y tipos compartidos por toda la app
│   │   ├── _index.ts                 # Barrel export del módulo common
│   │   │
│   │   ├── cmd/                      # Comandos RPC del microservicio (constantes de mensajes)
│   │   │   ├── _index.ts
│   │   │   ├── constant.ts           # CMDS: árbol de constantes de mensajes TCP
│   │   │   ├── interface.ts          # Interfaces base de comandos
│   │   │   └── interfaces/           # Comandos por dominio
│   │   │       ├── _index.ts
│   │   │       ├── auth.ts           # Comandos: auth.companies.*, auth.validate.*
│   │   │       ├── commercial.ts     # Comandos: commercial.contracts.*
│   │   │       ├── common.ts         # Comandos comunes/compartidos
│   │   │       ├── integrations.ts   # Comandos: integrations.email.*
│   │   │       └── logs.ts           # Comandos: logs.legacy.*
│   │   │
│   │   ├── functions/                # Funciones utilitarias puras
│   │   │   ├── _index.ts
│   │   │   ├── api-response.ts       # errResponseFn / successResponseFn
│   │   │   ├── identity.ts           # Función identidad genérica
│   │   │   └── logger.ts             # LOG: logging con colores ANSI y contexto
│   │   │
│   │   └── interfaces/               # Tipos e interfaces compartidas
│   │       ├── _index.ts
│   │       ├── api-response.ts       # IApiSuccessResponse, IApiErrorResponse, TApi<T>
│   │       ├── option.ts             # IOption<T>
│   │       ├── option-extended.ts    # IOptionExtended<T>
│   │       ├── pagination.ts         # IPagination
│   │       └── jobs/                 # Tipos para trabajos asíncronos/colas
│   │           ├── _index.ts
│   │           ├── email/
│   │           │   ├── _index.ts
│   │           │   └── pdf.ts        # IJobEmailPdf — payload para emails con PDF
│   │           └── internal/
│   │               ├── _index.ts
│   │               └── notification.ts  # IJobInternalNotification
│   │
│   ├── config/                       # Configuración centralizada del entorno
│   │   ├── _index.ts
│   │   ├── environments.ts           # Validación con Joi: HOST, PORT, DATABASE_URL
│   │   └── transport.ts              # Configuración de transporte TCP
│   │
│   ├── contracts/                    # Contratos tipados para comunicación entre microservicios
│   │   ├── _index.ts
│   │   ├── system.ts                 # Tipos del sistema (⚠️ Pendiente de verificar contenido)
│   │   ├── types.ts                  # TContractSend<C,R>, TContractEmit<C> — base de contratos
│   │   │
│   │   ├── auth/                     # Contrato del microservicio de autenticación (este ms)
│   │   │   ├── _index.ts
│   │   │   ├── contract.ts           # Definición del contrato MsAuth
│   │   │   ├── schema.ts             # ICompany, IKey, IAuth
│   │   │   └── interfaces/
│   │   │       ├── _index.ts
│   │   │       ├── companies.ts      # search-one, search-all
│   │   │       └── validation.ts     # create-key, generate-signature, validate-key, validate-authorization, validate-legacy
│   │   │
│   │   ├── commercial/               # Contrato del microservicio comercial
│   │   │   ├── _index.ts
│   │   │   ├── contract.ts           # Definición del contrato MsCommercial
│   │   │   └── interfaces/
│   │   │       ├── _index.ts
│   │   │       └── contracts.ts      # create, search-*, change-limit, change-balance
│   │   │
│   │   ├── integrations/             # Contrato del microservicio de integraciones externas
│   │   │   ├── _index.ts
│   │   │   ├── contract.ts           # Definición del contrato MsIntegrations
│   │   │   └── interfaces/
│   │   │       ├── _index.ts
│   │   │       └── email.ts          # notification
│   │   │
│   │   └── logs/                     # Contrato del microservicio de logs
│   │       ├── _index.ts
│   │       ├── contract.ts           # Definición del contrato MsLogs
│   │       └── interfaces/
│   │           ├── _index.ts
│   │           └── legacy.ts         # create, update, search-id, search-user, search-terms
│   │
│   └── core/                         # Núcleo de la aplicación — providers globales
│       ├── _index.ts
│       ├── module.ts                 # CoreModule global — provee PrismaService
│       ├── repositories/
│       │   └── _index.ts             # 🚧 Vacío — estructura lista para implementar repositorios
│       └── services/
│           ├── _index.ts
│           └── prisma.ts             # PrismaService — extiende PrismaClient, conecta en init
│
├── .dockerignore
├── .env-template                     # Variables de entorno requeridas: HOST, PORT, DATABASE_URL
├── .eslintignore
├── .gitignore
├── .prettierignore
├── .prettierrc                       # Configuración de Prettier
├── eslint.config.mjs                 # Configuración de ESLint (flat config)
├── nest-cli.json                     # Config del CLI de NestJS
├── package.json                      # Dependencias y scripts del proyecto
├── package-lock.json
├── README.md                         # ⚠️ Contenido pendiente de relevar
├── tsconfig.json                     # TypeScript: target ES2023, strict mode
├── tsconfig.build.json               # Config de compilación para producción
└── tsconfig.paths.json               # Path aliases: @common, @config, @core, @contracts, @db
```

---

## Anotaciones por zona

| Zona | Archivos | Propósito |
|------|----------|-----------|
| `src/main.ts` + `src/module.ts` | 2 | Bootstrap y módulo raíz |
| `src/common/` | ~18 | Tipos, funciones y constantes compartidas |
| `src/config/` | 3 | Configuración y variables de entorno |
| `src/contracts/` | ~20 | Contratos tipados para 4 microservicios |
| `src/core/` | 4 | Providers globales (Prisma) |
| `prisma/` | 1 + generados | Esquema y cliente ORM |
| `docker/` | 2 | Infraestructura de contenedores |
| `.github/workflows/` | 2 | CI/CD |

---

## Observaciones

- 💀 `src/core/repositories/_index.ts` — archivo vacío, sin implementación. Estructura reservada para el patrón Repository.
- ⚠️ No existen carpetas `controllers/`, `handlers/`, `resolvers/` ni `routes/`. Los handlers RPC **no están implementados** en este repositorio — los contratos están definidos pero sin lógica de negocio.
- ⚠️ No hay archivos de test (`.spec.ts`, `.test.ts`).
- ⚠️ No hay migraciones Prisma commitadas (`prisma/migrations/` ausente).
- 🔒 `.env-template` expone los nombres de las variables requeridas sin valores — correcto.
- 📦 `prisma/generated/client/` debería estar en `.gitignore` (generado automáticamente).

---

## Leyenda

| Ícono | Significado |
|-------|-------------|
| 🔄 | Proceso automático / pipeline |
| ⚠️ | Advertencia / pendiente de verificar |
| 🚧 | En construcción / sin implementar |
| 💀 | Código muerto / archivo vacío sin uso |
| 🔒 | Afecta seguridad |
| 📦 | Dependencia externa |
