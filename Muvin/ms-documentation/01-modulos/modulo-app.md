---
tags: [módulo, bootstrap, raíz]
última-revisión: 2026-04-27
---

# Módulo: AppModule

> **Ruta/Namespace:** `src/module.ts`
> **Criticidad:** 🟡 Media
> **Estado:** Activo

## Propósito

Módulo raíz de la aplicación NestJS. Actúa como punto de ensamblado: importa el `CoreModule` y, en el futuro, todos los módulos de dominio. No contiene lógica de negocio.

## Funcionalidades que expone

Ninguna directamente. Es el contenedor top-level.

## Dependencias

- **Depende de:** [[modulo-core]]
- **Es usado por:** `main.ts` (punto de bootstrap)

## Diagrama

```mermaid
graph TD
    MAIN[main.ts] -->|NestFactory.createMicroservice| APP[AppModule]
    APP -->|imports| CORE[CoreModule]
    APP -.->|futuros| DOM1[MóduloDominio1]
    APP -.->|futuros| DOM2[MóduloDominio2]
```

## Archivos fuente relevantes

- `src/module.ts`

## Notas

> [!info] Punto de extensión
> Para agregar un nuevo módulo de dominio, importarlo en el array `imports` de este módulo.
