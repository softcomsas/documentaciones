---
tags: [inventario, dependencias, módulos]
última-revisión: 2026-04-27
---

# Dependencias entre módulos internos

## Diagrama de dependencias internas

```mermaid
graph LR
    MAIN[main.ts]
    APP[AppModule]
    CORE[CoreModule]
    PRISMASVC[PrismaService]
    CONFIG[config/environments]
    TRANSPORT[config/transport]
    CONTRACTS[src/contracts]
    COMMON[src/common]

    MAIN --> APP
    MAIN --> CONFIG
    MAIN --> TRANSPORT
    APP --> CORE
    CORE --> PRISMASVC
```

## Tabla de dependencias de módulos NestJS

| Módulo | Depende de | Es usado por |
|--------|-----------|--------------|
| `AppModule` | `CoreModule` | `main.ts` |
| `CoreModule` | `PrismaService` | `AppModule` (y cualquier módulo futuro vía global) |
| `PrismaService` | `@db` (Prisma Client generado) | `CoreModule` |

## Dependencias de path aliases

| Alias | Resuelve a | Usado actualmente por |
|-------|-----------|----------------------|
| `@common` | `src/common/_index` | Futuros módulos de dominio |
| `@config` | `src/config/_index` | `main.ts` |
| `@core` | `src/core/_index` | `AppModule` |
| `@contracts` | `src/contracts/_index` | Futuros handlers de mensajes |
| `@db` | `prisma/generated/client` | `PrismaService` |

## Dependencias circulares

No se detectaron dependencias circulares. La arquitectura es estrictamente jerárquica en su estado actual.

## Dependencias con microservicios externos (por contrato)

```mermaid
graph LR
    CPE["muvin-ms-cpe"]
    AUTH["MsAuth"]
    LOGS["MsLogs"]
    COMMERCIAL["MsCommercial"]
    INTEGRATIONS["MsIntegrations"]

    CPE -->|"send (TCP)"| AUTH
    CPE -->|"emit + send (TCP)"| LOGS
    CPE -->|"send (TCP)"| COMMERCIAL
    CPE -->|"emit (TCP)"| INTEGRATIONS
```

> [!warning] Dependencias externas aún no activas
> Los contratos en `src/contracts/` definen las interfaces pero **ningún `ClientProxy` está implementado aún**. Las dependencias externas se activarán cuando se implementen los módulos de dominio.
