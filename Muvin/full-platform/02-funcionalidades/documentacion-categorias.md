# F-12 — Gestión de Categorías

## Descripción

Módulo de administración de **categorías de documentación** dentro de `documentacion-app`. Permite crear, editar y eliminar categorías que luego se asignan a los documentos del centro de documentación.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend | `documentacion-app/frontend/src/app/modules/categoria/` |
| Backend | `documentacion-app/backend/` |

## Funcionalidades

- **Listar** categorías existentes en tabla
- **Crear** nueva categoría (`nuevaCategoria/` sub-módulo con formulario)
- **Editar** categoría seleccionada
- **Eliminar** categoría (con confirmación)

## Estructura del módulo

```
categoria/
├── categoria.component.ts      ← lógica principal
├── categoria.component.html    ← tabla de categorías
├── categoria.module.ts
├── categoria.routing.module.ts
└── nuevaCategoria/             ← diálogo/formulario de creación/edición
```

## Ruta Angular

`/documentacion/categorias`

## Relación con otros módulos

- Las categorías creadas aquí se utilizan en **F-11 Centro de Documentación** al crear nuevos documentos o presentaciones.
- Sin categorías, el centro de documentación no puede clasificar documentos.
