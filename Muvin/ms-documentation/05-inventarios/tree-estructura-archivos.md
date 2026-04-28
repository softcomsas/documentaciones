---
tags: [inventario, estructura, árbol]
última-revisión: 2026-04-27
---

# Árbol de estructura de archivos

> **Proyecto:** `muvin-ms-cpe` (ms-documentation)
> **Tipo:** Microservicio NestJS · TCP puro

## Árbol completo

```
ms-documentation/
├── .dockerignore
├── .env-template                    # Plantilla de variables de entorno requeridas
├── .eslintignore
├── .gitignore
├── .prettierrc
├── .github/
│   └── workflows/
│       ├── deploy-dev.yml           # CI/CD → rama dev → Docker → SSH deploy
│       └── sync-cap.yml             # Sincronización con repositorio externo (rama cap)
├── .husky/
│   └── pre-commit                   # Hook: lint-staged antes de cada commit
├── docker/
│   ├── Dockerfile                   # Multi-stage build (builder + production)
│   └── docker-compose.yml           # Solo MySQL 8.0 local; servicio app comentado
├── prisma/
│   └── schema.prisma                # ⚠️ Vacío — sin modelos definidos aún
├── src/
│   ├── main.ts                      # Bootstrap: NestJS Microservice TCP en HOST:PORT
│   ├── module.ts                    # AppModule raíz — solo importa CoreModule
│   ├── common/                      # Utilidades compartidas (sin lógica de negocio)
│   │   ├── _index.ts
│   │   ├── cmd/
│   │   │   ├── _index.ts
│   │   │   ├── constant.ts          # CMDS: todos los message patterns del ecosistema
│   │   │   ├── interface.ts
│   │   │   └── interfaces/
│   │   │       ├── auth.ts          # Interfaces de mensajes auth
│   │   │       ├── commercial.ts    # Interfaces de mensajes commercial
│   │   │       ├── common.ts        # Interfaces comunes
│   │   │       ├── integrations.ts  # Interfaces de mensajes integrations
│   │   │       └── logs.ts          # Interfaces de mensajes logs
│   │   ├── functions/
│   │   │   ├── api-response.ts      # successResponseFn / errResponseFn
│   │   │   ├── identity.ts          # IDENTITY<T>: función identidad genérica
│   │   │   └── logger.ts            # LOG(): logger con colores ANSI
│   │   └── interfaces/
│   │       ├── api-response.ts      # TApi<T>, TApiSuccessResponse, TApiErrorResponse
│   │       ├── option.ts            # IOption<T>
│   │       ├── option-extended.ts   # IOptionExtended<T>
│   │       ├── pagination.ts        # IPagination
│   │       └── jobs/
│   │           ├── email/
│   │           │   └── pdf.ts       # IJobEmailPdf
│   │           └── internal/
│   │               └── notification.ts  # IJobInternalNotification
│   ├── config/
│   │   ├── environments.ts          # Validación Joi de HOST, PORT, DATABASE_URL
│   │   └── transport.ts             # Exporta Transport.TCP = 0
│   ├── contracts/                   # SDK de tipos para comunicación inter-microservicios
│   │   ├── system.ts                # TContractSend<C,R>, TContractEmit<C>
│   │   ├── types.ts                 # TMethod, TLogsLegacyStatus, TCommercialContract*
│   │   ├── auth/
│   │   │   ├── contract.ts          # TContractAuth, TContractAuthCompanies, etc.
│   │   │   ├── schema.ts            # SchemeAuthCompany
│   │   │   └── interfaces/
│   │   │       ├── companies.ts
│   │   │       └── validation.ts
│   │   ├── commercial/
│   │   │   ├── contract.ts          # TContractCommercial, TContractCommercialContracts
│   │   │   └── interfaces/
│   │   │       └── contracts.ts
│   │   ├── integrations/
│   │   │   ├── contract.ts          # TContractIntegrations, TContractIntegrationsEmail
│   │   │   └── interfaces/
│   │   │       └── email.ts
│   │   └── logs/
│   │       ├── contract.ts          # TContractLogs, TContractLogsLegacy
│   │       └── interfaces/
│   │           └── legacy.ts
│   └── core/
│       ├── module.ts                # CoreModule @Global — registra PrismaService
│       ├── repositories/
│       │   └── _index.ts            # 💀 Vacío — repositorios pendientes de implementar
│       └── services/
│           └── prisma.ts            # PrismaService (extiende PrismaClient)
├── eslint.config.mjs
├── nest-cli.json
├── package.json                     # name: muvin-ms-cpe, v0.0.1
├── tsconfig.json
├── tsconfig.build.json
└── tsconfig.paths.json              # Aliases: @common, @config, @core, @contracts, @db
```

## Directorios generados (no versionados)

| Directorio | Generado por | Cuándo |
|-----------|-------------|--------|
| `prisma/generated/client/` | `npm run prisma:generate` | Antes de build o en CI |
| `dist/` | `npm run build` | En build de producción |
| `node_modules/` | `npm ci` / `npm install` | Al instalar dependencias |

## Aliases de path (`tsconfig.paths.json`)

| Alias | Resuelve a |
|-------|-----------|
| `@common` | `src/common/_index` |
| `@config` | `src/config/_index` |
| `@core` | `src/core/_index` |
| `@contracts` | `src/contracts/_index` |
| `@db` | `prisma/generated/client` |
