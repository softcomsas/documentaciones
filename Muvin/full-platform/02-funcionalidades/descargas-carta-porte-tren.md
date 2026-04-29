# F-03 — Carta Porte Tren

## Descripción

Wizard para generar una **Carta de Porte Electrónica para transporte ferroviario**. Flujo similar al de camión pero adaptado a los datos específicos del transporte en tren (número de vagón, empresa ferroviaria, ramal, etc.).

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `descargas-app/frontend/src/app/page/carta-porte-tren/` |
| Frontend (wizard) | `descargas-app/frontend/src/app/components/carta-porte-wizard-tren/` |
| Frontend (búsqueda) | `descargas-app/frontend/src/app/components/carta-porte-search-tren/` |
| Backend (controller) | `descargas-app/backend/src/controllers/tren.controller.ts` |
| Backend (servicio) | `descargas-app/backend/src/services/cp-tren.service.ts` |

## Ruta Angular

`/descargas/carta-porte-tren`

## Diferencias respecto a Carta Porte Camión

| Aspecto | Camión | Tren |
|---|---|---|
| Transporte | Patente + chofer | Número de vagón + empresa ferroviaria |
| Ruta | Libre | Ramal ferroviario |
| Controller BE | `descargas.controller.ts` | `tren.controller.ts` |
| Servicio BE | `descargas.service.ts` | `cp-tren.service.ts` |
| Reporte Excel | `GET /reportes/descargas` | `GET /reportes/tren` |

## Endpoints backend consumidos

| Método | Ruta | Descripción |
|---|---|---|
| `GET` | `/descargas/tren` | Listar cartas porte tren |
| `POST` | `/descargas/tren` | Crear carta porte tren |
| `PUT` | `/descargas/tren` | Actualizar estado |
| `GET` | `/reportes/tren` | Exportar Excel de cartas porte tren |

## Rol requerido

`DES Verificador CP Tren` (o similar)

## Consideraciones

- Los datos de tren incluyen campos como número de vagón, empresa ferroviaria y ramal, que no existen en la carta porte camión.
- El servicio `CartaPorteTrenService` en backend maneja la lógica específica ferroviaria.
- El reporte Excel de tren está incluido en `ReportesController` (ver F-06).
