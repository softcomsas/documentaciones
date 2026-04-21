# Matriz de Dependencias entre Módulos

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Ver también:** [[cross-module-dependencies]], [[functional-classification]]

---

## Convención de lectura

- La **fila** es el módulo que **depende de** la columna.
- La **columna** es el módulo del cual se depende.
- `✔` = hay dependencia directa (importación de código).
- `—` = no hay dependencia.

---

## Matriz

| Depende de →           | `main` | `module` | `config` | `common` | `modules/email` | `services` |
|------------------------|:------:|:--------:|:--------:|:--------:|:---------------:|:----------:|
| **`main`**             | —      | ✔        | —        | ✔        | —               | —          |
| **`module`**           | —      | —        | ✔        | ✔        | ✔               | ✔          |
| **`config`**           | —      | —        | —        | —        | —               | —          |
| **`common`**           | —      | —        | —        | —        | —               | —          |
| **`modules/email`**    | —      | —        | ✔        | ✔        | —               | ✔          |
| **`services`**         | —      | —        | —        | —        | —               | —          |

---

## Notas al pie

| Dependencia | Tipo | Detalle |
|------------|------|---------|
| `main` → `module` | Importación directa | `NestFactory.create(AppModule)` |
| `main` → `common` | Importación directa | Usa `LOG` de `@common` |
| `module` → `config` | Importación directa | Consume `ENVIRONMENTS` y `QUEUES` de `@config` |
| `module` → `common` | Importación directa | Consume `LOG` de `@common` |
| `module` → `modules/email` | Importación directa | Registra `EmailProcessor` como provider |
| `module` → `services` | Importación directa | Registra `PdfParserService` como provider |
| `modules/email` → `config` | Importación directa | Consume `QUEUES` de `@config` |
| `modules/email` → `common` | Importación directa | Consume `IJobEmailPdf` de `@common` |
| `modules/email` → `services` | Inyección de dependencias NestJS | `PdfParserService` inyectado en constructor |

---

## Análisis de acoplamiento

- **`common`** es la capa con mayor cantidad de dependientes (3 módulos): es el núcleo de tipos compartidos.
- **`config`** tiene 2 dependientes directos: `module` y `modules/email`.
- **`services`** tiene 2 dependientes: `module` (registro) y `modules/email` (uso).
- **`config`** y **`common`** y **`services`** son hojas del grafo (no dependen de ningún otro módulo interno) → bajo acoplamiento, alta cohesión.
- No hay dependencias circulares.
