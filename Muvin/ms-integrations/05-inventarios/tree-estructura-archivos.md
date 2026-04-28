# Árbol de Estructura de Archivos

> **Generado:** 2026-04-21
> **Herramienta:** Análisis manual del repositorio
> **Proyecto:** `muvin-ms-integrations`

---

## Árbol completo anotado

```
ms-integrations/
├── docker/                         # Infraestructura de contenedores
│   ├── docker-compose.yml          # ⚠️ Servicio principal comentado; solo levanta MySQL
│   └── Dockerfile                  # Multi-stage build: builder + production (node:20-alpine)
│
├── prisma/                         # ORM y esquema de base de datos
│   ├── schema.prisma               # Definición de modelos MySQL + enums + relaciones
│   └── migrations/                 # Historial de migraciones Prisma
│       ├── migration_lock.toml     # Lock de proveedor (mysql)
│       ├── 20251108205145/
│       │   └── migration.sql       # Migración inicial (tablas base)
│       ├── 20251109194039/
│       │   └── migration.sql       # Segunda migración
│       └── 20251109224454/
│           └── migration.sql       # Tercera migración
│
├── src/                            # Código fuente principal (TypeScript)
│   ├── main.ts                     # Bootstrap del microservicio TCP (puerto 4006)
│   ├── module.ts                   # AppModule raíz — importa CoreModule y GmailModule
│   │
│   ├── common/                     # Utilidades y contratos compartidos
│   │   ├── _index.ts               # Re-exportaciones del barrel
│   │   ├── cmd/                    # Comandos de microservicio (message patterns)
│   │   │   ├── _index.ts           # Barrel
│   │   │   ├── constant.ts         # CMDS: mapa de strings para MessagePattern
│   │   │   ├── interface.ts        # Interfaz ICMDS (tipado del mapa)
│   │   │   └── interfaces/         # Sub-interfaces por dominio
│   │   │       ├── _index.ts       # Barrel
│   │   │       ├── commercial.ts   # ICMDCommercial
│   │   │       ├── common.ts       # (vacío o tipos comunes)
│   │   │       ├── integrations.ts # ICMDIntegrations
│   │   │       └── logs.ts         # ICMDLogs
│   │   ├── functions/              # Funciones utilitarias
│   │   │   ├── _index.ts           # Barrel
│   │   │   ├── api-response.ts     # Helper para construir TApi<T>
│   │   │   ├── identity.ts         # ⚠️ Pendiente de verificar propósito exacto
│   │   │   └── logger.ts           # LOG(): wrapper coloreado sobre NestJS Logger
│   │   └── interfaces/             # Interfaces comunes reutilizables
│   │       ├── _index.ts           # Barrel
│   │       ├── api-response.ts     # TApi<T>, TApiSuccessResponse, TApiErrorResponse
│   │       ├── option-extended.ts  # ⚠️ Pendiente de verificar
│   │       ├── option.ts           # ⚠️ Pendiente de verificar
│   │       └── pagination.ts       # IPagination { page, limit }
│   │
│   ├── config/                     # Configuración y variables de entorno
│   │   ├── _index.ts               # Barrel
│   │   ├── environments.ts         # Validación Joi de variables de entorno (IEnvironment)
│   │   ├── queues.ts               # Definición de colas Bull (QUEUES)
│   │   └── transport.ts            # Transport.TCP = 0
│   │
│   ├── contracts/                  # Contratos de microservicios (tipado de mensajes)
│   │   ├── _index.ts               # Barrel y types de alto nivel
│   │   ├── system.ts               # TContractEmit, TContractSend, aliases de dominio
│   │   ├── types.ts                # Tipos literales: status, priority, métodos HTTP
│   │   ├── commercial/             # Contratos del microservicio de comercial
│   │   │   ├── _index.ts
│   │   │   ├── contract.ts         # MsCommercial interface
│   │   │   ├── schema.ts           # Schemes de entidades (IContract, IProduct)
│   │   │   └── interfaces/
│   │   │       ├── _index.ts
│   │   │       └── contracts.ts    # CommercialContracts (create, search-all, search-reference)
│   │   ├── integrations/           # Contratos del propio microservicio
│   │   │   ├── _index.ts
│   │   │   ├── contract.ts         # MsIntegrations interface
│   │   │   ├── schema.ts           # 💀 Archivo vacío o sin uso
│   │   │   └── interfaces/
│   │   │       ├── _index.ts
│   │   │       └── email.ts        # IntegrationsEmail (notification)
│   │   └── logs/                   # Contratos del microservicio de logs
│   │       ├── _index.ts
│   │       ├── contract.ts         # MsLogs interface
│   │       └── interfaces/
│   │           ├── _index.ts
│   │           └── legacy.ts       # LogsLegacy (create, update, search-id, search-user, search-terms)
│   │
│   ├── core/                       # Módulo de infraestructura global
│   │   ├── _index.ts               # Barrel (re-exporta services y repositories)
│   │   ├── module.ts               # CoreModule (@Global): registra Bull, Prisma, repos
│   │   ├── repositories/           # Acceso a datos — patrón Repository
│   │   │   ├── _index.ts           # Barrel
│   │   │   ├── gmail-accounts.ts   # GmailAccountsRepository
│   │   │   ├── gmail-credential-scopes.ts  # ⚠️ Pendiente de verificar (no inyectado en GmailService)
│   │   │   ├── gmail-credentials.ts        # GmailCredentialsRepository
│   │   │   ├── gmail-labels.ts             # GmailLabelsRepository (cache en memoria)
│   │   │   ├── gmail-messages.ts           # GmailMessagesRepository
│   │   │   └── gmail-scopes.ts             # ⚠️ Pendiente de verificar (no inyectado en GmailService)
│   │   └── services/               # Servicios de infraestructura
│   │       ├── _index.ts           # Barrel
│   │       ├── prisma.ts           # PrismaService (extiende PrismaClient)
│   │       └── queue.ts            # QueueService (wrapper Bull)
│   │
│   └── modules/                    # Módulos de negocio
│       └── gmail/                  # Único módulo funcional activo
│           ├── _index.ts           # Barrel
│           ├── controller.ts       # GmailController (MessagePattern)
│           ├── module.ts           # GmailModule
│           └── service.ts          # GmailService (lógica de negocio principal)
│
├── eslint.config.mjs               # Configuración ESLint (typescript-eslint + prettier)
├── nest-cli.json                   # Configuración NestJS CLI
├── package.json                    # Dependencias y scripts
├── README.md                       # README original (vacío o mínimo)
├── tsconfig.json                   # Configuración TypeScript base
├── tsconfig.build.json             # TypeScript para build (excluye tests)
└── tsconfig.paths.json             # Path aliases (@common, @config, @core, @contracts, @db)
```

---

## Path aliases configurados

| Alias | Resolución |
|---|---|
| `@common` | `src/common/_index` |
| `@config` | `src/config/_index` |
| `@core` | `src/core/_index` |
| `@contracts` | `src/contracts/_index` |
| `@db` | `prisma/generated/client` |
| `@repositories` | `src/repositories/_index.ts` ⚠️ ruta no existe en el workspace |

> [!warning] Alias fantasma
> `@repositories` apunta a `./src/repositories/_index.ts` que **no existe** en la estructura actual. No genera error en tiempo de ejecución solo porque no hay código que lo importe, pero es un riesgo de mantenimiento.

---

## Métricas de estructura

| Categoría | Cantidad |
|---|---|
| Módulos NestJS | 2 (CoreModule, GmailModule) |
| Controladores | 1 (GmailController) |
| Servicios de negocio | 1 (GmailService) |
| Repositories | 5 declarados, 4 inyectados |
| Servicios de infraestructura | 2 (Prisma, Queue) |
| Modelos Prisma | 5 |
| Migraciones | 3 |
| Archivos de contratos | ~12 |
| Total archivos `.ts` | ~40 |

---

## Ver también

- [[stack-tecnologico]]
- [[cross-module-dependencies]]
- [[modulo-core]]
- [[modulo-gmail]]
