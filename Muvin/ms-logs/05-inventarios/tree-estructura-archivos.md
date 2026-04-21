# Árbol de Estructura de Archivos — `muvin-ms-logs`

> **Generado:** 2026-04-21
> **Herramienta:** Inspección manual del workspace
> **Cobertura:** 100 % del repositorio

---

## Árbol completo anotado

```
ms-logs/
├── docker/                          # Configuración de contenedores
│   ├── docker-compose.yml           # Solo levanta MySQL; el MS está comentado (⚠️ incompleto)
│   ├── Dockerfile                   # Imagen de producción del MS
│   └── DOCKER.md                    # Guía de uso de Docker
│
├── prisma/                          # ORM y migraciones de base de datos
│   ├── schema.prisma                # Definición del modelo de datos (7 modelos, 3 enums)
│   └── migrations/
│       ├── migration_lock.toml      # Lock de proveedor (mysql)
│       └── 20251021181757_/
│           └── migration.sql        # Única migración — crea el esquema completo inicial
│
├── src/                             # Código fuente principal
│   ├── main.ts                      # Bootstrap del microservicio TCP
│   ├── module.ts                    # Módulo raíz (AppModule)
│   ├── service.ts                   # PrismaService — singleton de conexión a BD
│   │
│   ├── common/                      # Utilidades compartidas entre módulos
│   │   ├── _index.ts                # Barrel export de todo common
│   │   ├── cmd/
│   │   │   ├── _index.ts            # Barrel export
│   │   │   ├── constant.ts          # ⚠️ BUG: event.update tiene valor duplicado
│   │   │   └── interface.ts         # Tipado de la estructura CMDS
│   │   ├── enums/
│   │   │   ├── _index.ts
│   │   │   ├── graphql-operation.ts # EGraphQlOperation: QUERY | MUTATION
│   │   │   ├── method.ts            # EHttpMethod: GET | POST | PUT | DELETE | PATCH
│   │   │   └── status.ts            # EStatus: PENDING | SUCCESS | ERROR | TIMEOUT
│   │   └── functions/
│   │       ├── _index.ts
│   │       ├── identity.ts          # Función identidad genérica (utilidad mínima)
│   │       ├── logger.ts            # LOG() — wrapper coloreado sobre NestJS Logger
│   │       └── schema.ts            # ⚠️ Pendiente de verificar (no leído)
│   │
│   ├── config/
│   │   ├── _index.ts
│   │   └── environments.ts          # Validación de env vars con Joi + export tipado
│   │
│   ├── contracts/                   # Tipos compartidos de contratos entre servicios
│   │   ├── ms-legacy/               # Contrato del sistema legado externo
│   │   │   ├── _index.ts
│   │   │   ├── api.ts               # IApiResponse<T> — envelope de respuesta HTTP
│   │   │   ├── contract.ts          # TContract<TResponse, QP, Body> — tipo genérico
│   │   │   ├── requests.ts          # ⚠️ Pendiente de verificar
│   │   │   └── interfaces/
│   │   │       ├── _index.ts
│   │   │       └── comprador-by-razon-social.ts  # ⚠️ Pendiente de verificar
│   │   └── ms-logs/                 # Contrato público de este microservicio
│   │       ├── _index.ts
│   │       ├── contract.ts          # TContractMsLogs — 9 tipos de mensajes
│   │       ├── enums.ts             # EApi: LEGACY_PANEL | LEGACY_DESCARGAS
│   │       └── schema.ts            # Tipos base: TLogTrace, TLogEvent, TLogLegacy
│   │
│   ├── core/                        # Funciones de dominio reutilizables
│   │   ├── _index.ts
│   │   └── utils/
│   │       ├── _index.ts
│   │       ├── json.ts              # compressJsonFn / decompressJsonFn (Brotli Q11)
│   │       └── terms.ts             # extractSearchableFieldsFn / mergeSearchTermsFn
│   │
│   └── modules/                     # Módulos funcionales de NestJS
│       ├── legacy/                  # Módulo de logging del sistema legado
│       │   ├── controller.ts        # 5 MessagePatterns (create, update, search×3)
│       │   ├── module.ts            # LegacyModule
│       │   └── service.ts           # LegacyService — lógica CRUD + búsqueda
│       └── microservices/           # Módulo de trazas GraphQL y eventos de MS
│           ├── controller.ts        # 4 MessagePatterns (trace×2, event×2)
│           ├── module.ts            # MicroservicesModule
│           └── service.ts           # MicroservicesService — CRUD de traces/events
│
├── eslint.config.mjs                # Configuración ESLint (flat config)
├── nest-cli.json                    # Configuración CLI de NestJS
├── package.json                     # Dependencias y scripts
├── README.md                        # README básico (vacío o mínimo)
├── tsconfig.json                    # TypeScript base
├── tsconfig.build.json              # TypeScript para build de producción
└── tsconfig.paths.json              # Path aliases (@common, @core, @config, etc.)
```

---

## Resumen por carpeta

| Carpeta | Archivos | Propósito |
|---------|----------|-----------|
| `src/modules/` | 6 | Lógica de negocio — controladores y servicios |
| `src/contracts/` | 9 | Tipos de contrato inter-servicios |
| `src/common/` | 8 | Enums, CMDs, logger compartido |
| `src/core/` | 4 | Compresión Brotli y extracción de términos |
| `src/config/` | 2 | Variables de entorno con validación |
| `prisma/` | 3 | Esquema y migraciones de BD |
| `docker/` | 3 | Infraestructura local |

**Total archivos fuente TypeScript:** ~25
**Total modelos Prisma:** 7
**Total enums Prisma:** 3
**Total message patterns:** 9 (1 con bug)

---

## Aliases de path configurados (`tsconfig.paths.json`)

| Alias | Ruta real |
|-------|-----------|
| `@common` | `src/common/_index.ts` |
| `@core` | `src/core/_index.ts` |
| `@config` | `src/config/_index.ts` |
| `@contract-ms-logs` | `src/contracts/ms-logs/_index.ts` |
| `@contract-ms-legacy` | `src/contracts/ms-legacy/_index.ts` |

> ⚠️ Pendiente de verificar: la configuración exacta de `tsconfig.paths.json` (el archivo no fue leído directamente, los aliases se infieren de los imports).

---

## Hallazgos durante el relevamiento

| Severidad | Hallazgo | Archivo |
|-----------|----------|---------|
| 🔴 | `event.update` tiene el mismo valor que `event.create` (`'logs.event.create'`) | `src/common/cmd/constant.ts:17` |
| ⚠️ | `docker-compose.yml` tiene el servicio del MS comentado — no se puede orquestar completamente | `docker/docker-compose.yml` |
| ⚠️ | `src/common/functions/schema.ts` no fue leído — contenido desconocido | `src/common/functions/schema.ts` |
| ⚠️ | `src/contracts/ms-legacy/requests.ts` y `comprador-by-razon-social.ts` no fueron leídos | `src/contracts/ms-legacy/` |
| 💀 | `IDENTITY` en `common/functions/identity.ts` no tiene usos detectados en este MS | `src/common/functions/identity.ts` |

---

*Ver también: [[stack-tecnologico]] · [[cross-module-dependencies]] · [[security-inventory]]*
