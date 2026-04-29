# Árbol de archivos — muvin-api

> **Última revisión:** 2026-04-29

```
api/
├── package.json              # muvin-api, NestJS 11, dependencias
├── nest-cli.json             # Configuración CLI NestJS
├── tsconfig.json             # TypeScript base
├── tsconfig.build.json       # TypeScript para build
├── tsconfig.paths.json       # Path aliases (@common, @core, @config, etc.)
├── eslint.config.mjs         # ESLint flat config
├── README.md
├── docker/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── DOCKER.md
├── nginx-legacy.conf         # Config Nginx (legacy)
└── src/
    ├── main.ts               # ⚙️ Bootstrap: pipes, filtros, interceptores, puerto
    ├── module.ts             # Root AppModule: GraphQlModule + RestModule
    │
    ├── config/
    │   ├── _index.ts         # Re-export environments
    │   └── environments.ts   # Variables de entorno tipadas (Joi validation)
    │
    ├── common/               # Utilidades compartidas (alias: @common)
    │   ├── _index.ts
    │   ├── cmd/
    │   │   ├── constant.ts   # CMDS: nombres de mensajes a microservicios
    │   │   ├── interface.ts
    │   │   └── _index.ts
    │   ├── enums/            # Enums del dominio
    │   ├── functions/
    │   │   └── LOG()         # Función de logging
    │   └── interfaces/       # Interfaces compartidas
    │
    ├── core/                 # Enhancers NestJS (alias: @core)
    │   ├── _index.ts
    │   ├── filters/
    │   │   └── AllExceptionsFilter    # Filtro global de excepciones
    │   ├── interceptors/
    │   │   └── GraphqlLoggingInterceptor  # Log de operaciones GraphQL
    │   ├── cache/            # Utilidades de caché
    │   └── dtos/             # DTOs compartidos del core
    │
    ├── contracts/            # Tipos de contratos con microservicios
    │   ├── ms-legacy/        # IRequests para ms-legacy
    │   └── ms-logs/          # TContractMsLogs, TLogLegacy
    │
    └── modules/
        ├── graphql.ts        # GraphQlModule: Apollo + LegacyModule
        ├── rest.ts           # RestModule: LogsModule + TemporaryModule
        │
        ├── legacy/           # Módulo GraphQL → ms-legacy
        │   ├── module.ts     # Registra ClientProxy TCP + MICROSERVICE_INTERCEPTOR
        │   ├── resolver.ts   # @Query legacy(rs, page) → BuyerEntity[]
        │   └── entities/
        │       └── buyer.entity.ts  # @ObjectType BuyerEntity {id, rs, cuit}
        │
        ├── logs/             # Módulo REST → ms-logs
        │   ├── module.ts     # Registra ClientsModule TCP
        │   ├── controller.ts # 5 endpoints REST (POST, PUT, GET x3)
        │   ├── service.ts    # emit/send a ms-logs via ClientProxy
        │   └── dtos/
        │       ├── create.ts
        │       ├── update.ts
        │       ├── search-id.ts
        │       ├── search-user.ts
        │       ├── search-terms.ts
        │       └── _index.ts
        │
        └── temporary/        # Módulo REST → descargas-app (proxy HTTP)
            ├── module.ts     # Registra HttpModule (Axios, sin timeout, 100MB)
            ├── controller.ts # POST /descargas → service.query(body)
            ├── service.ts    # Auth en 2 pasos + proxy HTTP
            ├── configuration.ts  # ⚠️ URLs y credenciales hardcodeadas
            └── interfaces.ts     # ILoginPayload/Response, IAccessPayload/Response
```
