# Endpoints GraphQL — muvin-api

> **URL:** `POST /api/graphql`
> **Driver:** Apollo Server 4 + ApolloDriver NestJS
> **Estrategia:** Code-first (genera `src/schema.gql` automáticamente)
> **Playground:** Apollo Sandbox (solo local)

---

## Query: `legacy`

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

### Ejemplo de uso

**Request:**
```graphql
query {
  legacy(rs: "Muvin", page: 1) {
    id
    rs
    cuit
  }
}
```

**Response:**
```json
{
  "data": {
    "legacy": [
      { "id": 42, "rs": "Muvin SRL", "cuit": "30-12345678-0" }
    ]
  }
}
```

### Errores GraphQL

| Escenario | Error retornado |
|-----------|----------------|
| ms-legacy no disponible | `"No data received"` envuelto en errors GraphQL |
| Error de conexión TCP | `Internal server error` |

---

## Schema generado

El archivo `src/schema.gql` se genera automáticamente con cada build. **No editar manualmente.**

---

## Configuración Apollo

```typescript
GraphQLModule.forRoot<ApolloDriverConfig>({
  driver: ApolloDriver,
  debug: true,
  playground: false,                              // Deshabilitado
  autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
  plugins: [ApolloServerPluginLandingPageLocalDefault()],  // Apollo Sandbox
})
```

> ⚠️ `debug: true` en producción puede exponer stack traces en las respuestas GraphQL.

---

## Referencias

- [[modulo-legacy]]
- [[f01-legacy-query]]
