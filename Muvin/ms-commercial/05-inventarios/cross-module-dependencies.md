# Dependencias Entre Módulos

**Última actualización:** 2026-04-27

## Gráfico de Dependencias

```
┌──────────────────┐
│   app.module.ts  │ (raíz)
└────────┬─────────┘
         │
         ├──────────────┬────────────────┬─────────────────┐
         │              │                │                 │
         ▼              ▼                ▼                 ▼
    ConfigModule  CoreModule(@Global) ContractsModule ValidationModule
                      │                    │                │
                      │                    │                │
        ┌─────────────┼────────┬───┐     │                │
        │             │        │   │     │                │
        ▼             ▼        ▼   ▼     ▼                ▼
   PrismaService  ContractsRepo  ... Contracts    Validation
                  EntitiesRepo        Service     Service
                  ProductsRepo
                  KeysRepo
```

---

## Tabla de Dependencias

| Módulo | Depende de | Proporciona |
|--------|-----------|------------|
| **CoreModule** | PrismaService (NestJS) | PrismaService, Repositories |
| **ContractsModule** | CoreModule (repositories) | ContractsService |
| **ValidationModule** | CoreModule (repositories) | ValidationService |

---

## Inyección de Dependencias

```
ContractsService
├─ ContractsRepository → PrismaService
├─ EntitiesRepository → PrismaService
└─ ProductsRepository → PrismaService

ValidationService
├─ KeysRepository → PrismaService
└─ EntitiesRepository → PrismaService
```

---

## Sin Dependencias Circulares

- ✅ No hay circular imports
- ✅ DAG (Directed Acyclic Graph) limpio
- ✅ Fácil testing y reemplazo de módulos

---

## Documentos Relacionados

- [[tree-estructura-archivos|Árbol de Archivos]] - Dónde está cada código
- [[../01-modulos/_indice-modulos|Índice de Módulos]] - Descripción de módulos

