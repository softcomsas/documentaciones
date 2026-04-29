# F-09 — Tablero de Ofertas

## Descripción

Vista de tipo **dashboard/reporte** que presenta las ofertas de compra/venta de granos disponibles en el mercado. Permite al usuario visualizar y analizar las ofertas vigentes de forma consolidada.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `oferta-app/frontend/src/app/page/tablero/` |
| Backend | `oferta-app/backend/` |

## Ruta Angular

`/oferta/tablero`

## Funcionalidades

- **Tabla de ofertas** con datos de precio, volumen, producto, condición de entrega, plazo y empresa
- **Filtros** por producto, plazo, condición de entrega y empresa
- **Vista de mercado**: permite comparar ofertas de distintas partes
- **Actualización** de datos desde la fuente externa de ofertas
- **Indicadores visuales** de variación de precios respecto al día anterior

## Diferencia con Gestión de Ofertas (F-10)

| Aspecto | Tablero (F-09) | Gestión (F-10) |
|---|---|---|
| Tipo | Lectura / reporte | CRUD |
| Usuario | Consulta del mercado | Administración de sus propias ofertas |
| Ruta | `/oferta/tablero` | `/oferta/ofertas` |

## Consideraciones

- El tablero es de **solo lectura**: no permite crear ni modificar ofertas directamente
- Los datos pueden provenir de una fuente externa (API de mercado) o de la propia BD de Muvin
