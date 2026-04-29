# F-06 — Reportes Excel

## Descripción

Exportación de datos a archivos Excel (.xlsx) para los distintos tipos de operaciones de `descargas-app`. Los reportes se generan en el backend y se descargan directamente desde el navegador.

## Ubicación

| Capa | Ruta |
|---|---|
| Backend (controller) | `descargas-app/backend/src/controllers/reportes.controller.ts` |
| Backend (servicio) | `descargas-app/backend/src/services/reportes.service.ts` |
| Backend (util) | `descargas-app/backend/src/utils/excel/excel.service.ts` |

## Endpoints disponibles

| Método | Ruta | Datos exportados |
|---|---|---|
| `GET` | `/reportes/descargas` | Cupos/descargas con todos los filtros |
| `GET` | `/reportes/rt` | Transferencias de cupo (RT) |
| `GET` | `/reportes/adendas` | Adendas |
| `GET` | `/reportes/tren` | Cartas porte tren |

## Parámetros del GET /reportes/descargas

Los mismos filtros que `GET /descargas`:
`fechaInicio`, `fechaFin`, `idEstadoSCE`, `ctg`, `idCupoTerminal`, `producto`, `procesados`, `destinatario`, `destino`, `idMotivosError`

## Tecnología

- **ExcelService** (`excel.service.ts`) genera el archivo `.xlsx` usando una librería de hojas de cálculo (probablemente `exceljs` o `xlsx`)
- El backend responde con el archivo como `Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`
- El frontend descarga el archivo directamente usando `Response` de Express

## Flujo

```
1. Usuario aplica filtros en la pantalla de Gestión de Cupos
2. Hace clic en "Exportar Excel"
3. Frontend llama GET /reportes/descargas con los mismos filtros
4. Backend consulta todos los datos (sin paginación)
5. ExcelService genera el archivo .xlsx
6. Response retorna el archivo para descarga directa
```
