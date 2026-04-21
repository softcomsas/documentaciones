# Árbol de estructura de archivos

> **Proyecto:** `muvin-ms-legacy`
> **Generado:** 2026-04-21
> **Herramienta:** inspección manual del repositorio

## Árbol completo anotado

```
ms-legacy/                                  ← Raíz del microservicio
├── docker/                                 ← Infraestructura de contenedores
│   ├── docker-compose.yml                  ← Orquestación local/producción
│   ├── Dockerfile                          ← Build multi-stage (builder + production)
│   └── DOCKER.md                           ← Documentación de uso Docker
│
├── src/                                    ← Código fuente TypeScript
│   ├── main.ts                             ← Bootstrap: crea el microservicio TCP
│   ├── module.ts                           ← AppModule raíz de NestJS
│   ├── controller.ts                       ← AppController: entrada de mensajes TCP
│   ├── service.ts                          ← AppService: proxy HTTP al backend legacy
│   │
│   ├── api/                                ← Capa de definición de queries HTTP
│   │   ├── interface.ts                    ← IQueries: contrato del mapa de queries
│   │   ├── map.ts                          ← QUERIES_MAP: registro de queries activas
│   │   └── queries/                        ← Una implementación por query
│   │       ├── _index.ts                   ← Re-exporta todas las queries
│   │       └── comprador-by-razon-social.ts ← Única query implementada actualmente
│   │
│   ├── common/                             ← Utilidades compartidas internas
│   │   ├── _index.ts                       ← Barrel export de common
│   │   ├── functions/
│   │   │   ├── _index.ts                   ← Barrel export de functions
│   │   │   ├── identity.ts                 ← Función IDENTITY<T>(x) => x (pass-through)
│   │   │   └── logger.ts                   ← Wrapper sobre NestJS Logger con colores ANSI
│   │   ├── interfaces/
│   │   │   ├── _index.ts                   ← Barrel export de interfaces
│   │   │   ├── option.ts                   ← IOption<T>: estructura genérica de select ⚠️ sin uso
│   │   │   └── option-extended.ts          ← IOptionExtended<T>: extiende IOption ⚠️ sin uso
│   │   └── types/
│   │       ├── _index.ts                   ← Barrel export de types
│   │       ├── graphql-operation.ts        ← TGraphQlOperation 💀 código muerto (no hay GraphQL)
│   │       ├── http-method.ts              ← THttpMethod 💀 duplica TMethod en types/
│   │       └── status.ts                   ← TStatus: estados de operación ⚠️ sin uso visible
│   │
│   ├── config/
│   │   ├── _index.ts                       ← Barrel export de config
│   │   └── environments.ts                 ← Carga y valida variables de entorno (Joi)
│   │
│   ├── contracts/                          ← Contratos del microservicio (API pública)
│   │   └── ms-legacy/
│   │       ├── _index.ts                   ← Barrel export del contrato completo
│   │       ├── api.ts                      ← IApiResponse<T>, IMeta (respuesta estándar)
│   │       ├── contract.ts                 ← TContract<TResponse, TQueryParams, TBody>
│   │       ├── requests.ts                 ← IRequests: mapa de endpoints disponibles
│   │       └── interfaces/
│   │           ├── _index.ts               ← Barrel export de interfaces del contrato
│   │           └── comprador-by-razon-social.ts ← Tipos de request/response del endpoint
│   │
│   └── types/                              ← Tipos internos del sistema de proxy
│       ├── _index.ts                       ← Barrel export de todos los types
│       ├── adapters.ts                     ← TAdapter<Res, Result>: función de transformación
│       ├── client.ts                       ← TClient<K>: tipo del payload enviado al legacy
│       ├── endpoints.ts                    ← TEndpoint: unión de rutas válidas al backend
│       ├── err.ts                          ← TErr<K>: tipo del error del backend
│       ├── methods.ts                      ← TMethod: verbos HTTP permitidos
│       ├── params.ts                       ← TParams<K>: parámetros genéricos de una query
│       ├── queries.ts                      ← TQueryKey, TQueries, TQueriesValue
│       ├── request.ts                      ← TRequest<...>: definición completa de una query
│       ├── res.ts                          ← TRes<K>: tipo de respuesta cruda del backend
│       ├── result.ts                       ← TResult<K>: tipo de resultado transformado
│       └── server.ts                       ← TServer<K>: tipo del payload transformado
│
├── eslint.config.mjs                       ← Configuración ESLint (flat config)
├── nest-cli.json                           ← Configuración NestJS CLI
├── package.json                            ← Dependencias y scripts NPM
├── README.md                               ← ⚠️ README vacío o sin contenido relevante
├── tsconfig.json                           ← Configuración TypeScript principal
├── tsconfig.build.json                     ← Config TS para build (excluye tests)
└── tsconfig.paths.json                     ← Path aliases (@common, @config, @types, etc.)
```

## Resumen por sección

| Carpeta | Archivos | Propósito | Estado |
|---------|----------|-----------|--------|
| `src/` (raíz) | 4 | Bootstrap y módulo NestJS | 🟢 Activo |
| `src/api/` | 4 | Registro y definición de queries HTTP | 🟡 Incompleto (1 query de 2 posibles) |
| `src/common/` | 8 | Utilidades, logger, tipos genéricos | 🟡 Contiene código muerto |
| `src/config/` | 2 | Variables de entorno con validación | 🟢 Activo |
| `src/contracts/` | 5 | API pública del microservicio | 🟢 Activo |
| `src/types/` | 11 | Sistema de tipos para el proxy HTTP | 🟢 Activo |
| `docker/` | 3 | Infraestructura de contenedor | 🟢 Activo |

## Notas sobre archivos problemáticos

| Archivo | Problema | Severidad |
|---------|----------|-----------|
| `src/common/types/graphql-operation.ts` | `TGraphQlOperation` definido, no hay GraphQL en el proyecto | 💀 Código muerto |
| `src/common/types/http-method.ts` | `THttpMethod` duplica `src/types/methods.ts::TMethod` | 💀 Duplicado |
| `src/common/interfaces/option.ts` | `IOption<T>` sin referencias en el código | ⚠️ Sin uso |
| `src/common/interfaces/option-extended.ts` | `IOptionExtended<T>` sin referencias | ⚠️ Sin uso |
| `src/types/endpoints.ts` | `persona-rol/comprador-by-cuit` declarado pero sin implementación | ⚠️ Incompleto |
| `src/controller.ts` | `console.log()` expone payload y respuesta en logs | 🔴 Seguridad |
| `README.md` (raíz) | Sin contenido técnico útil | ⚠️ Pendiente |
