# Índice de Servicios Backend — muvin-api

> **Última revisión:** 2026-04-29

---

## REST Endpoints

Base path global: `/api`

| Método | Ruta | Módulo | Descripción |
|--------|------|--------|-------------|
| POST | `/api/logs` | logs | Crear log (fire-and-forget) |
| PUT | `/api/logs` | logs | Actualizar log (fire-and-forget) |
| GET | `/api/logs/by-id` | logs | Buscar log por ID |
| GET | `/api/logs/by-user` | logs | Buscar logs por usuario |
| GET | `/api/logs/by-terms` | logs | Buscar logs por términos |
| POST | `/api/descargas` | temporary | Proxy hacia descargas-app |

## GraphQL

| Tipo | Operación | Módulo | Descripción |
|------|-----------|--------|-------------|
| Query | `legacy(rs, page)` | legacy | Compradores por razón social |

**Endpoint GraphQL:** `POST /api/graphql`
**Playground:** Apollo Sandbox (local) — `ApolloServerPluginLandingPageLocalDefault`

---

## Archivos de detalle

- [[graphql-endpoints]]
- [[rest-endpoints]]
