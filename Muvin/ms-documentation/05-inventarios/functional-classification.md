---
tags: [inventario, clasificación, módulos]
última-revisión: 2026-04-27
---

# Clasificación funcional de módulos

> **Estado del proyecto:** Base estructural implementada. Sin módulos de dominio aún.

## Clasificación actual

| Componente | Tipo funcional | Estado | Descripción breve |
|------------|---------------|--------|-------------------|
| `AppModule` | Configuración | ✅ Activo | Módulo raíz; solo ensambla el CoreModule |
| `CoreModule` | Utilitario / Infraestructura | ✅ Activo | Módulo global; provee PrismaService |
| `PrismaService` | Infraestructura / Datos | ✅ Activo | Cliente de base de datos MySQL |
| `src/contracts/` | Integración / SDK de tipos | ✅ Activo | Contratos de comunicación inter-microservicios |
| `src/common/cmd/` | Utilitario | ✅ Activo | Constantes de message patterns |
| `src/common/functions/` | Utilitario | ✅ Activo | Helpers de respuesta y logging |
| `src/common/interfaces/` | Utilitario / Tipos | ✅ Activo | Interfaces compartidas del sistema |
| `src/config/` | Configuración | ✅ Activo | Validación de entorno y transporte |
| `src/core/repositories/` | Datos | 🚧 Pendiente | Vacío — repositorios no implementados |

## Distribución por tipo

```mermaid
pie title Clasificación funcional de componentes
    "Utilitario" : 3
    "Configuración" : 2
    "Infraestructura / Datos" : 2
    "Integración / SDK" : 1
    "Pendiente" : 1
```

## Distribución esperada al completar features

```mermaid
pie title Clasificación proyectada con módulos de dominio
    "Dominio / CRUD" : 4
    "Utilitario" : 3
    "Configuración" : 2
    "Infraestructura / Datos" : 3
    "Integración / SDK" : 1
```

> [!warning] Proyecto en estado inicial
> El microservicio tiene la estructura base completa pero **ningún módulo de dominio implementado**. El esquema Prisma está vacío, los repositorios están pendientes, y no hay `@MessagePattern` handlers registrados.
