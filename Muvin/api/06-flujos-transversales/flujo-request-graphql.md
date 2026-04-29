# Flujo: Query GraphQL completa

> **Aplica a:** módulo `legacy`
> **Última revisión:** 2026-04-29

---

## Ciclo de vida de una query GraphQL

```mermaid
sequenceDiagram
    autonumber
    participant C as Cliente
    participant AP as Apollo Server
    participant GLI as GraphqlLoggingInterceptor
    participant AEF as AllExceptionsFilter
    participant RES as LegacyResolver
    participant CP as ClientProxy (TCP)
    participant MS as ms-legacy

    C->>AP: POST /api/graphql\n{query: "{ legacy(rs:...) {...} }"}
    AP->>GLI: Interceptar operación GraphQL
    GLI->>GLI: Registrar operación
    AP->>RES: Invocar resolver legacy(rs, page)

    RES->>CP: send('', {endpoint, params})
    CP->>MS: TCP message

    alt ms-legacy no responde
        MS-->>CP: Timeout / Error
        CP-->>RES: Error
        RES-->>AEF: Excepción
        AEF-->>AP: Error normalizado
        AP-->>C: {errors: [{message: "..."}]}
    else Respuesta sin data
        MS-->>CP: {data: null}
        CP-->>RES: value.data = null
        RES->>RES: throw Error('No data received')
        RES-->>AP: Error GraphQL
        AP-->>C: {errors: [{message: "No data received"}]}
    else Éxito
        MS-->>CP: {data: [...compradores]}
        CP-->>RES: value
        RES->>RES: plainToInstance(BuyerEntity, value.data)
        RES-->>AP: [BuyerEntity]
        AP-->>C: {data: {legacy: [...]}}
    end
```

---

## Schema generado (code-first)

NestJS genera `src/schema.gql` automáticamente al arrancar/compilar:

```graphql
type BuyerEntity {
  id: Int!
  rs: String!
  cuit: String!
}

type Query {
  legacy(rs: String!, page: Int): [BuyerEntity!]!
}
```

> El archivo `schema.gql` **no debe editarse manualmente** — se sobreescribe en cada build.

---

## GraphqlLoggingInterceptor

Interceptor global registrado en `main.ts` que actúa sobre **todas las operaciones GraphQL**:

```typescript
app.useGlobalInterceptors(new GraphqlLoggingInterceptor());
```

Registra cada operación (query/mutation) para trazabilidad.

---

## Referencias

- [[arquitectura-general]]
- [[modulo-legacy]]
- [[f01-legacy-query]]
