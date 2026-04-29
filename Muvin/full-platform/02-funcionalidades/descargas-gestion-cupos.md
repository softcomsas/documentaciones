# F-01 — Gestión de Cupos

## Descripción

Pantalla principal de `descargas-app`. Permite visualizar, filtrar y gestionar todos los cupos de descarga de granos del usuario. Es el punto de entrada desde donde se inician el resto de flujos (carta porte, adenda, transferencia).

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend | `descargas-app/frontend/src/app/page/descargas/` |
| Backend | `descargas-app/backend/src/controllers/descargas.controller.ts` |
| Servicio BE | `descargas-app/backend/src/services/descargas.service.ts` |

## Ruta Angular

`/descargas`

## Funcionalidades de la pantalla

- **Tabla paginada** de cupos con columnas: fecha, CTG, producto, destinatario, destino, estado SCE, estado procesado
- **Filtros**: fechaInicio, fechaFin, idEstadoSCE, CTG, idCupoTerminal, producto, procesados, destinatario, destino, idMotivosError
- **Actualizar cupo desde AFIP**: sincroniza el estado del cupo con AFIP en tiempo real
- **Acciones por fila**: iniciar carta porte camión, carta porte tren, adenda, transferencia
- **Carga de cupos via TXT**: abre dialog `UploadFileComponent`
- **Tabs**: organiza la vista por estados o tipos de cupo

## Endpoints backend consumidos

| Método | Ruta | Descripción |
|---|---|---|
| `GET` | `/descargas` | Listar cupos con filtros y paginación |
| `POST` | `/descargas` | Crear o actualizar cupos |
| `PUT` | `/descargas` | Actualizar estado de un cupo |
| `POST` | `/descargas/actualizar-cupo-afip` | Sincronizar cupos con AFIP |

## Parámetros del GET /descargas

| Parámetro | Tipo | Descripción |
|---|---|---|
| `fechaInicio` | string | Fecha inicio del rango |
| `fechaFin` | string | Fecha fin del rango |
| `idEstadoSCE` | string | Estado SCE del cupo |
| `ctg` | number | Número CTG |
| `idCupoTerminal` | string | ID terminal del cupo |
| `producto` | string | Tipo de grano |
| `procesados` | boolean | Filtrar por procesados |
| `page` | number | Página actual |
| `pageSize` | number | Tamaño de página |
| `destinatario` | string | Empresa destinataria |
| `destino` | string | Planta de destino |
| `idMotivosError` | string | Filtro por motivo de error |

## Componentes Angular utilizados

- `TableDinamicComponentV2` / `TableDinamicV21Component` — tabla paginada dinámica
- `FilterButtonComponent` — botones de filtro
- `HeaderOneComponent` — encabezado de página
- `SearchComponent` — barra de búsqueda
- `UploadFileComponent` — dialog de carga TXT
- `MatTabsModule`, `MatDialogModule`, `MatDatepickerModule`

## Rol requerido

Acceso controlado por guards de autenticación JWT. El servicio `LocalService` gestiona el contexto del usuario logueado.
