# Matriz de Dependencias

> **Última revisión:** 2026-04-29
> **Scope:** Dependencias entre módulos frontend/fullstack del monorepo

## Matriz NxN

Las **filas** son los módulos que **consumen** (dependen de).
Las **columnas** son los módulos que **proveen** (son consumidos por).

| Módulo ↓ depende de → | `shared` | `main` | `auth-app` | `descargas-app` | `logistica-app` | `oferta-app` | `documentacion-app` | `pedidos-app` | `superapp` |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| `shared` | — | | | | | | | | |
| `main` | ✔¹ | — | | | | | | | |
| `auth-app` | ✔¹ | | — | | | | | | |
| `descargas-app` | ✔¹ | | | — | | | | | |
| `logistica-app` | ✔¹ | | | | — | | | | |
| `oferta-app` | ✔¹ | | | | | — | | | |
| `documentacion-app` | ✔¹ | | | | | | — | | |
| `pedidos-app` | ✔¹ | | | | | | | — | |
| `superapp` | ✔¹ | | | | | | | | — |

> ✔¹ **Importación directa** — importa librerías de `shared/data-access-user`, `shared/frontend/auth`, `shared/frontend/global-setting`, `shared/frontend/ux-ui-components` mediante paths de Nx definidos en `tsconfig.base.json`.

---

## Dependencias de Runtime (Module Federation)

| Host | Remote cargado | Tipo |
|---|---|---|
| `main` | `auth-app` | ✔² Runtime MFE |
| `main` | `descargas-app` | ✔² Runtime MFE |
| `main` | `logistica-app` | ✔² Runtime MFE |
| `main` | `oferta-app` | ✔² Runtime MFE |
| `main` | `documentacion-app` | ✔² Runtime MFE |
| `main` | `pedidos-app` | ✔² Runtime MFE |
| `main` | `superapp` | ✔² Runtime MFE |

> ✔² **Runtime MFE** — carga dinámica en el browser via `loadRemoteModule()`. No hay importación de código en tiempo de compilación del shell.

---

## Notas al Pie

| Código | Tipo de dependencia | Descripción |
|---|---|---|
| ✔¹ | Importación directa | Importa módulos TS/Angular por path de Nx |
| ✔² | Runtime MFE | Carga dinámica via Module Federation en runtime |
| ⚠️ DB compartida | A verificar | ⚠️ Pendiente — se debe confirmar si NestJS y Yii2 usan la misma instancia de DB |
| ⚠️ Archivo compartido | `environments/` | Variables de entorno compartidas en la raíz del monorepo |
