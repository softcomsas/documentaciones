# Matriz de dependencias entre módulos

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21
> **Lectura:** la fila **usa** a la columna. `✔` = dependencia directa.

## Matriz NxN

| | AppController | AppService | QUERIES_MAP | Query: comprador-by-rs | @common | @config | @types | @contract-ms-legacy |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **AppController** | — | ✔ | | | ✔ | ✔ | ✔ | ✔ |
| **AppService** | | — | ✔ | | ✔ | ✔ | ✔ | ✔ |
| **QUERIES_MAP** | | | — | ✔ | | | ✔ | |
| **Query: comprador-by-rs** | | | | — | ✔ | | ✔ | ✔ |
| **@common** | | | | | — | | | |
| **@config** | | | | | | — | | |
| **@types** | | | | | | | — | |
| **@contract-ms-legacy** | | | | | | | | — |

## Tipo de dependencia por celda

| Fila → Columna | Tipo |
|----------------|------|
| AppController → AppService | Inyección de dependencia (DI) NestJS |
| AppController → @common | Importación de función (`LOG`) |
| AppController → @config | Importación de constante (`environments`) |
| AppController → @types | Importación de tipo TypeScript |
| AppController → @contract-ms-legacy | Importación de tipo TypeScript |
| AppService → QUERIES_MAP | Importación de constante (Map) |
| AppService → @common | Importación de función (`LOG`) |
| AppService → @config | Importación de constante (`environments`) |
| AppService → @types | Importación de tipos TypeScript |
| AppService → @contract-ms-legacy | Importación de tipo TypeScript |
| QUERIES_MAP → Query: comprador-by-rs | Importación de constante (objeto de query) |
| QUERIES_MAP → @types | Importación de tipo (`TQueryKey`) |
| Query → @common | Importación de función (`IDENTITY`) |
| Query → @types | Importación de tipo (`TRequest`) |
| Query → @contract-ms-legacy | Importación de tipo (`IRequests`) |

## Observaciones

- **Sin dependencias circulares:** el grafo es un DAG (Directed Acyclic Graph) limpio.
- **Alta concentración en @types y @contract-ms-legacy:** 3 de 4 módulos activos los usan. Son el núcleo del sistema de tipado.
- **@common es poco cohesivo:** contiene código útil (`LOG`, `IDENTITY`) mezclado con código muerto (`IOption`, `TGraphQlOperation`).
