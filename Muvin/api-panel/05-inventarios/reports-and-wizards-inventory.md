# Inventario de Reportes y Wizards — Muvinapp

> **Última revisión:** 2026-04-21
> **Ver también:** [[functional-classification]], [[_indice-modulos]]

---

## Reportes identificados

| # | Nombre | Propósito | Archivo origen | Datos consumidos | Módulo | Estado |
|---|--------|-----------|----------------|-----------------|--------|--------|
| 1 | **Reporte de Cupos (Excel)** | Exportación de cupos filtrados a Excel | `backend/controllers/CupoController.php` → `actionReporteCupos` | Tabla `cupos`, filtros por fecha/producto/estado | Cupos | 🟢 Activo |
| 2 | **PDF de Arribes** | PDF con detalle de arribes intermedios para imprimir | `backend/controllers/PdfArribosController.php` | `ArribosIntermedios`, `Cupo`, `Chofer`, `Centro` | Arribo/Calada | 🟢 Activo |
| 3 | **Cupo PDF (CCPP)** | PDF del cupo / carta de porte para el chofer | `backend/modules/v3/controllers/CupoController.php` → `actionObtenerCupoPdf` | Cupo, CartaPorte, intervinientes | V3 / CCPP | 🟢 Activo |
| 4 | **Reporte MAGYP / Seguimiento** | Reporte de seguimiento de cartas de porte MAGYP | `backend/modules/magyp/controllers/ReporteController.php` → `actionSeguimiento` | Módulo MAGYP, datos de contacto, cadena | MAGYP | 🟢 Activo |
| 5 | **Carta de Porte imprimible** | PDF de carta de porte electrónica para el viaje | `common/components/PdfResources.php` + `CartaPorte` model | `CartaPorte`, `Cupo`, actores intervinientes | AFIP/CCPP | 🟢 Activo |
| 6 | **Reporte de Carátulas MTR** | Listado Excel/JSON de carátulas del mercado a término | `backend/modules/mtr/controllers/DefaultController.php` → `actionCaratulaReporte` | `CaratulaMtr`, MATba API | MTR | 🟡 Uso variable |
| 7 | **Excel de Generación masiva** | Generación de reportes Excel masivos | `common/components/GenerarExcel.php` | Varía según endpoint | Varios | 🟢 Activo |
| 8 | **Manual de usuario** | Documento de ayuda en línea | `backend/controllers/ManualController.php` | Archivo(s) estático(s) | Admin | ⚠️ Pendiente de verificar |

---

## Wizards / Asistentes identificados

| # | Nombre | Propósito | Endpoint de entrada | Módulo | Tipo | Estado |
|---|--------|-----------|---------------------|--------|------|--------|
| 1 | **Carga masiva de cupos** | Carga de múltiples cupos desde archivo/API | `POST v1/cargaCupos` → `api-cupo/carga-cupos` | Cupos | Batch/Import | 🟢 Activo |
| 2 | **Asignación de cupo (v1)** | Flujo de asignación de cupo a chofer/viaje | `POST v1/asignarCupo` → `api-cupo/asignar-cupo` | Cupos | Proceso | 🟢 Activo |
| 3 | **Asignación de cupo (v2)** | Flujo de asignación v2 | `POST v2/cupos/asignar` | V2 | Proceso | 🟡 Legacy |
| 4 | **Chatbot — Crear Pedido** | Wizard por chat para crear un pedido | `ChatBotController` | Bot | Wizard | 🟢 Activo |
| 5 | **Chatbot — Demanda de Cupo** | Wizard por chat para demandar cupo | `ChatBotController` | Bot | Wizard | 🟢 Activo |
| 6 | **Chatbot — Solicitud Combustible** | Wizard por chat para retiro de combustible | `ChatBotController` | Bot | Wizard | 🟢 Activo |
| 7 | **Carga de solicitud (v2)** | Carga de solicitud de transporte | `POST v2/cupos/carga-solicitud` | V2 | Proceso | 🟡 Legacy |
| 8 | **Concurso / Sorteo** | Registro y gestión de concurso con ganadores | `ConcursoController`, `GanadoresSorteoController` | Admin | Wizard/CRUD | ⚠️ Pendiente de verificar |
| 9 | **Edición múltiple CCPP** | Editar intervinientes de múltiples cartas de porte | `PUT v3/ccpp/intervinientes/multiples` | V3 | Proceso | 🟢 Activo |

---

## Notas

- La generación de Excel usa `common/components/GenerarExcel.php` que wrappea PHPSpreadsheet 1.18.0.
- La generación de PDF usa `common/components/PdfResources.php` con kartik-v/yii2-mpdf.
- ⚠️ No existe un inventario centralizado de reportes en el código. Los reportes están dispersos en controladores individuales, identificados por acciones con nombres como `reporte`, `pdf`, `excel`, `download`.
- 🚧 El Manual de usuario (`ManualController`) requiere verificación: no está claro si sirve un archivo estático o genera contenido dinámico.
