# Inventario de Reportes y Wizards

> **Última revisión:** 2026-04-29

## Reportes

| # | Nombre | Propósito | Archivos origen | Datos consumidos | Módulo | Estado |
|---|---|---|---|---|---|---|
| R-01 | Reporte de Cupos/Descargas | Exportación Excel de cupos y descargas en un período | `descargas-app/backend/src/services/reportes.service.ts`, `controllers/reportes.controller.ts` | Cupos, descargas, carta porte | [[modulo-descargas]] | ✅ Activo |
| R-02 | Reporte Carta Porte | Reporte de cartas porte generadas | `descargas-app/backend/src/services/cp-tren.service.ts` (probable) | CartaPorteTren, archivos adjuntos | [[modulo-descargas]] | ⚠️ Pendiente de verificar alcance |
| R-03 | Reporte de Adendas | ⚠️ Pendiente de verificar si existe endpoint dedicado | `descargas-app/backend/src/services/adenda.service.ts` | Adendas, archivos | [[modulo-descargas]] | ⚠️ Pendiente |
| R-04 | Reporte de Transferencias | ⚠️ Pendiente de verificar | `descargas-app/backend/src/services/transferencia.service.ts` | Transferencias | [[modulo-descargas]] | ⚠️ Pendiente |
| R-05 | Tablero de Ofertas | Vista de resumen de ofertas activas (UI, no Excel) | `oferta-app/frontend/src/app/page/tablero/` | Ofertas activas | [[modulo-oferta]] | ✅ Activo |
| R-06 | Reportes Yii2 (logística) | ⚠️ Pendiente de verificar si Yii2 genera reportes propios | `logistica-app/backend/api/source/` | Viajes, cargas, equipos | [[modulo-logistica]] | ⚠️ Pendiente |
| R-07 | Reportes Yii2 (documentación) | ⚠️ Pendiente de verificar | `documentacion-app/backend/api/source/` | Documentos, vencimientos | [[modulo-documentacion]] | ⚠️ Pendiente |

## Wizards / Asistentes

| # | Nombre | Propósito | Archivos origen | Módulo | Estado |
|---|---|---|---|---|---|
| W-01 | Carga de Cupos (formulario multi-paso) | Wizard para ingresar cupos de descarga con validación AFIP | `descargas-app/frontend/src/app/page/descargas/`, modelo `CupoForm` | [[modulo-descargas]] | ✅ Activo |
| W-02 | Generación de Carta Porte (camión) | Wizard para generar carta porte de transporte camión | `descargas-app/frontend/src/app/page/carta-porte/` | [[modulo-descargas]] | ✅ Activo |
| W-03 | Generación de Carta Porte (tren) | Wizard para generar carta porte de transporte tren | `descargas-app/frontend/src/app/page/carta-porte-tren/` | [[modulo-descargas]] | ✅ Activo |
| W-04 | Carga de Adenda | Formulario de creación de adenda asociada a descarga | `descargas-app/frontend/src/app/page/adenda/` | [[modulo-descargas]] | ✅ Activo |
| W-05 | Transferencia de Cupo | Wizard de transferencia entre terminales | `descargas-app/frontend/src/app/page/transferencia/` | [[modulo-descargas]] | ✅ Activo |
| W-06 | Carga de Documento (documentacion) | Wizard de carga y clasificación de documentos | `documentacion-app/frontend/src/app/modules/centro-documentacion/` | [[modulo-documentacion]] | ✅ Activo |
| W-07 | Creación de Oferta | Formulario de alta de oferta comercial | `oferta-app/frontend/src/app/page/ofertas/` | [[modulo-oferta]] | ✅ Activo |
| W-08 | Alta de Viaje (logística) | Formulario de creación de viaje con carga | `logistica-app/frontend/src/app/page/proveedor/` | [[modulo-logistica]] | ✅ Activo |

## Procesos Batch / Automáticos

| # | Nombre | Propósito | Archivos origen | Módulo | Estado |
|---|---|---|---|---|---|
| B-01 | CronJob — ⚠️ nombre pendiente | Tarea automática programada (frecuencia y acción exacta ⚠️ pendiente de verificar) | `descargas-app/backend/src/services/cron.service.ts`, `modules/cron.module.ts`, `controllers/cron.controller.ts` | [[modulo-descargas]] | ✅ Activo (🔄) |

## Notas

- Los reportes Excel de descargas usan **ExcelJS** (`exceljs` v4.4.x) vía `reportes.service.ts`.
- Los reportes de los backends Yii2 pueden usar las vistas PHP nativas o generación de CSV/Excel propia; requiere revisión del código fuente PHP.
- El `CronModule` en descargas tiene un controlador HTTP propio (`cron.controller.ts`) que posiblemente permite disparar el cron manualmente — riesgo de seguridad si no está protegido adecuadamente.
