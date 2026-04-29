# Arquitectura General — muvin-api

> **Última revisión:** 2026-04-29

---

## Flujo de request completo

```mermaid
sequenceDiagram
    autonumber
    participant C as Cliente<br/>(App/Panel)
    participant NF as NestJS Framework<br/>(Global Enhancers)
    participant VP as ValidationPipe
    participant AEF as AllExceptionsFilter
    participant CTRL as Controller / Resolver
    participant SVC as Service
    participant EXT as Microservicio / HTTP externo

    C->>NF: HTTP Request (REST o GraphQL)
    NF->>VP: Validar y transformar DTO
    alt Validación fallida
        VP-->>C: 400 Bad Request + errores
    else OK
        VP->>CTRL: DTO validado
        CTRL->>SVC: Llamada al servicio
        SVC->>EXT: ClientProxy.send/emit o Axios
        EXT-->>SVC: Respuesta / Observable
        SVC-->>CTRL: Datos transformados
        CTRL-->>C: Respuesta JSON / GraphQL
    end
    Note over AEF: Captura cualquier error no manejado<br/>y lo normaliza antes de responder
```

---

## Estructura de módulos

```mermaid
graph TD
    subgraph AppModule
        subgraph GraphQlModule
            APOLLO_CFG[GraphQLModule.forRoot\nApolloDriver\ncode-first\nautoSchemaFile]
            LEGACY[LegacyModule\nLegacyResolver]
        end
        subgraph RestModule
            LOGS[LogsModule\nLogsController\nLogsService]
            TEMP[TemporaryModule\nTemporaryController\nTemporaryService]
        end
    end

    LEGACY -->|TCP ClientProxy| MS_LEG[(ms-legacy)]
    LOGS -->|TCP ClientProxy\nemit / send| MS_LOGS[(ms-logs)]
    TEMP -->|HTTPS Axios| PANEL_APP[(panel.muvinapp.com\n/descargas)]
```

---

## Capas de la aplicación

| Capa | Archivos | Responsabilidad |
|------|---------|----------------|
| **Entry point** | `main.ts` | Bootstrap, pipes globales, filtros, interceptores |
| **Root Module** | `module.ts` | Composición de GraphQlModule + RestModule |
| **Module layer** | `modules/*/module.ts` | Registro de controladores, servicios, clientes TCP |
| **Controller/Resolver** | `*/controller.ts`, `*/resolver.ts` | Routing HTTP/GraphQL, recepción de DTOs |
| **Service layer** | `*/service.ts` | Lógica de orquestación, llamadas a microservicios |
| **Core** | `core/` | Filtros, interceptores, caché, DTOs compartidos |
| **Common** | `common/` | Funciones utilitarias, enums, CMDs, interfaces |
| **Contracts** | `contracts/` | Tipos compartidos con microservicios (ms-legacy, ms-logs) |
| **Config** | `config/` | Variables de entorno tipadas |

---

## Enhancers globales

| Enhancer | Tipo | Descripción |
|----------|------|-------------|
| `ValidationPipe` | Pipe | Valida y transforma DTOs (class-validator). `whitelist: true`, `forbidNonWhitelisted: true` |
| `AllExceptionsFilter` | Filter | Captura toda excepción no manejada y retorna respuesta estandarizada |
| `GraphqlLoggingInterceptor` | Interceptor | Registra operaciones GraphQL (queries, mutations) |

---

## Límites de payload

Configurados en `main.ts`:

```typescript
app.use(json({ limit: '50mb' }));
app.use(urlencoded({ limit: '50mb', extended: true }));
```

> Fueron aumentados de 1 MB a 50 MB, probablemente para soportar el módulo de descargas.

---

## Referencias

- [[vision-general]]
- [[stack-tecnologico]]
- [[_indice-modulos]]
