# Inventario: Matriz de Dependencias

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## Leyenda

- `✔` — dependencia directa confirmada en código fuente
- `→` — dependencia indirecta (a través de un módulo intermediario)
- ` ` — sin dependencia
- `[TCP]` — dependencia por transporte TCP (runtime, no compile-time)
- `[imp]` — importación directa TypeScript
- `[NestJS]` — inyección de dependencias NestJS
- `[Prisma]` — acceso a BD mediante cliente generado

---

## Matriz de dependencias internas

Las **filas** son el módulo **origen** (quien depende). Las **columnas** son el módulo **destino** (de quien dependen).

| Origen ↓ \ Destino → | main.ts | AppModule | CoreModule | PrismaService | config/ | common/ | contracts/ | cmd/CMDS |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **main.ts** | — | ✔ [imp] | | | ✔ [imp] | | | |
| **AppModule** | | — | ✔ [NestJS] | | | | | |
| **CoreModule** | | | — | ✔ [NestJS] | | | | |
| **PrismaService** | | | | — | | | | |
| **config/** | | | | | — | | | |
| **common/functions** | | | | | | — | | |
| **common/interfaces** | | | | | | — | | |
| **contracts/auth** | | | | | | ✔ [imp] | — | ✔ [imp] |
| **contracts/commercial** | | | | | | ✔ [imp] | — | ✔ [imp] |
| **contracts/logs** | | | | | | ✔ [imp] | — | ✔ [imp] |
| **contracts/integrations** | | | | | | ✔ [imp] | — | ✔ [imp] |
| **cmd/CMDS** | | | | | | | | — |

---

## Matriz de dependencias hacia microservicios externos

| Origen (ms-auth) ↓ \ Destino → | ms-commercial | ms-logs | ms-integrations |
|---|:---:|:---:|:---:|
| **contracts/commercial** | ✔ [TCP] | | |
| **contracts/logs** | | ✔ [TCP] | |
| **contracts/integrations** | | | ✔ [TCP] |

---

## Análisis de centralidad

| Módulo | Fan-in (es usado por) | Fan-out (usa a) | Observación |
|--------|----------------------|-----------------|-------------|
| `common/` | 4 módulos (todos los contracts) | 0 internos | Núcleo compartido — alta cohesión |
| `cmd/CMDS` | 4 módulos (todos los contracts) | 0 internos | Tabla de comandos — cambios tienen alto impacto |
| `CoreModule` | AppModule | PrismaService | Punto de ensamblaje global |
| `PrismaService` | CoreModule (→ toda la app) | prisma/generated/client | Único acceso a BD |
| `contracts/auth` | 0 internos (es el contrato expuesto) | common, CMDS | Interfaz pública del microservicio |
| `AppModule` | main.ts | CoreModule | Módulo raíz |

---

## Dependencias problemáticas detectadas

| Dependencia | Tipo de problema | Severidad |
|---|---|---|
| `contracts/` → `cmd/CMDS` (strings) | Acoplamiento por constantes de string — error silencioso si el destino cambia el nombre del comando | 🟡 Medio |
| `PrismaService` → `prisma/generated/client` | El cliente generado no está versionado — `prisma generate` debe ejecutarse en cada setup | 🟡 Medio |
| `ms-auth` → `ms-commercial/logs/integrations` (TCP) | Acoplamiento por host:puerto — sin service discovery | 🟡 Medio |
