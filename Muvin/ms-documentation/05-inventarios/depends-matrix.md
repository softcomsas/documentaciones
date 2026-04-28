---
tags: [inventario, matriz, dependencias]
última-revisión: 2026-04-27
---

# Matriz de dependencias entre módulos

## Módulos internos actuales

| Módulo ↓ depende de → | AppModule | CoreModule | PrismaService | config | common | contracts |
|-----------------------|:---------:|:----------:|:-------------:|:------:|:------:|:---------:|
| **AppModule** | — | ✔ imports | — | — | — | — |
| **CoreModule** | — | — | ✔ provider | — | — | — |
| **PrismaService** | — | — | — | — | — | — |
| **config** | — | — | — | — | — | — |
| **common** | — | — | — | — | — | — |
| **contracts** | — | — | — | — | — | — |

**Leyenda:**
- `✔ imports` — importación NestJS (`imports: [...]`)
- `✔ provider` — inyección de dependencias NestJS (`providers: [...]`)
- `✔ inject` — uso vía constructor injection (se usará en módulos de dominio)

## Dependencias hacia microservicios externos

| Este MS ↓ consume → | MsAuth | MsLogs | MsCommercial | MsIntegrations |
|--------------------|:------:|:------:|:------------:|:--------------:|
| **muvin-ms-cpe** | ✔ send | ✔ emit+send | ✔ send | ✔ emit |

> [!info]
> Las celdas vacías en la matriz interna reflejan que el proyecto está en estado inicial. A medida que se agreguen módulos de dominio, la matriz se actualizará.
