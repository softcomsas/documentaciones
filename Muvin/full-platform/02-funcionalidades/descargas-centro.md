# F-08 — Centro de Descargas

## Descripción

Vista consolidada que muestra el estado de todas las operaciones de descarga del usuario: cupos activos, cartas porte emitidas, adendas, transferencias. Funciona como dashboard operativo para el seguimiento diario.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `descargas-app/frontend/src/app/page/centro-descargas/` |
| Backend | `descargas-app/backend/src/controllers/descargas.controller.ts` |

## Ruta Angular

`/descargas/centro-descargas`

## Funcionalidades

- **Vista unificada** de cupos, cartas porte, adendas y transferencias
- **Indicadores de estado**: pendientes, procesados, con error
- **Acciones rápidas**: desde cada fila acceder al detalle o continuar el flujo
- **Filtros** por fecha, tipo de operación y estado
- **Navegación** hacia los wizards de carta porte, adenda o transferencia

## Relación con otros módulos

El Centro de Descargas es la pantalla "hub" de `descargas-app`. Desde aquí el usuario puede acceder a:

- F-02 Carta Porte Camión
- F-03 Carta Porte Tren
- F-04 Adendas
- F-05 Transferencias
- F-06 Reportes Excel
- F-07 Carga TXT

## Consideraciones

La distinción entre "Gestión de Cupos" (F-01) y "Centro de Descargas" (F-08) es que:
- **F-01** se enfoca en los cupos cargados (datos de AFIP)
- **F-08** se enfoca en las operaciones generadas a partir de esos cupos (cartas porte, adendas, RT)
