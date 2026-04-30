# Matriz de Dependencias — App Agronomy

> **Última revisión:** 2026-04-30
> Filas = módulo origen (quien depende). Columnas = módulo destino (de quien depende).
> `✔` = dependencia directa. `~` = dependencia implícita/indirecta. En blanco = sin dependencia.

|  | AppModule | SessionsModule | PedidosModule | TransportistasModule | SharedModule | CentroAuthGuard |
|--|:---------:|:--------------:|:-------------:|:--------------------:|:------------:|:---------------:|
| **AppModule** | — | ✔ (lazy) | ✔ (lazy) | ✔ (lazy) | ✔ | ✔ |
| **SessionsModule** | | — | | | ✔ | |
| **PedidosModule** | | | — | ~ (modelos) | ✔ | |
| **TransportistasModule** | | | | — | ✔ | |
| **SharedModule** | | | | | — | |
| **CentroAuthGuard** | | | | | ~ (Router) | — |

## Notas al pie

- `~` **PedidosModule → TransportistasModule**: no hay importación directa del módulo, pero sí se reutilizan definiciones de modelos (`camion.ts`, `chofer.ts`) que están duplicadas en `pages/pedidos/models/`. Refactorizar a un único modelo en `shared/models/`.
- `~` **CentroAuthGuard → SharedModule**: el guard usa `Router` de Angular, no del shared, pero conceptualmente forma parte de la infraestructura compartida.
