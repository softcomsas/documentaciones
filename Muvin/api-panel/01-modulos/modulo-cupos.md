# Módulo Cupos (v1) — Gestión Core de Cupos

> **Última revisión:** 2026-04-21
> **Ruta:** `backend/controllers/Cupo*.php`, `ApiCupoController.php`
> **Ver también:** [[modulo-v3]], [[modulo-v2]], [[flujo-alta-cupo]]

---

## Propósito

El dominio **Cupos** es el núcleo de negocio de Muvinapp. Un cupo representa la autorización de un centro/terminal para recibir un camión con determinado producto en una fecha específica.

Este documento cubre los controladores del módulo v1 (no versionados) relacionados con cupos.

---

## Controladores principales

| Controlador | Propósito |
|-------------|-----------|
| `CupoController.php` | CRUD base del cupo + acciones de negocio |
| `ApiCupoController.php` | API especializada — asignación, carga masiva, lógica de negocio |
| `CupoClienteController.php` | Vista de cupos para clientes |
| `CupoFertilizanteController.php` | Cupos específicos de fertilizantes (v1) |
| `DemandaCupoController.php` | Gestión de demandas de cupo |
| `DemandaCaratulaController.php` | Carátulas de demanda (relación con MTR) |
| `DemandaEstadoController.php` | Estados de las demandas |
| `GestionadorCupoController.php` | Operaciones de gestión avanzada de cupos |
| `ActualizarPagoTasaController.php` | Actualización de pagos y tasas en cupos |

---

## Endpoints de ApiCupoController

| Acción | Método | Ruta | Propósito |
|--------|--------|------|-----------|
| `actionAsignarCupo` | POST | `POST v1/asignarCupo` | Asignar cupo a chofer |
| `actionCargaCupos` | POST | `POST v1/cargaCupos` | Carga masiva de cupos |
| `actionAsignarDestinatario` | POST | — | Asignar destinatario a cupo |
| `actionReporteCupos` | GET | — | Reporte Excel de cupos |

---

## Modelo de datos del Cupo (campos principales)

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK | Identificador único |
| `id_centro` | INT FK | Centro/terminal destino |
| `id_producto` | INT FK | Producto (soja, maíz, trigo...) |
| `fecha_entrega` | DATE | Fecha de descarga programada |
| `cantidad` | INT | Cantidad de cupos disponibles |
| `estado` | ENUM | Estado del cupo (ver estados) |
| `id_dador` | INT FK | Dador del cupo (vendedor) |
| `id_destinatario` | INT FK | Destinatario (comprador/planta) |
| `cupo_afip` / `alfanumerico` | VARCHAR | Código CTG de AFIP |
| `created_at` / `updated_at` | DATETIME | Timestamps |

---

## Estados del cupo

| Estado | Descripción |
|--------|-------------|
| `disponible` | Listo para asignación |
| `demandado` | Transportista ha solicitado este cupo |
| `asignado` | Asignado a un chofer/camión específico |
| `en_viaje` | Camión en tránsito |
| `entregado` | Descarga completada |
| `devuelto` | Cupo liberado de vuelta al pool |
| `anulado` | Cancelado |

---

## V2 vs V3 vs V1 — comparativa

| Característica | V1 | V2 (legacy) | V3 (actual) |
|---------------|----|-----------  |-------------|
| Carta de porte electrónica | ❌ | ❌ | ✅ |
| Integración AFIP/MAGYP | Parcial | ❌ | ✅ |
| Zonas geográficas | Básico | Básico | ✅ Avanzado |
| Contratos | ❌ | ❌ | ✅ |
| Uso actual | 🟡 Mixto | 🟡 Legacy | 🟢 Principal |
