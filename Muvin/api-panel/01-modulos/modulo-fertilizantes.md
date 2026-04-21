# Módulo Fertilizantes

> **Última revisión:** 2026-04-21
> **Namespace:** `fertilizantes\`
> **Ruta:** `backend/modules/fertilizantes/`
> **Ver también:** [[modulo-v3]], [[_indice-modulos]]

---

## Propósito

El módulo **fertilizantes** gestiona la operativa específica de **cupos de fertilizantes**, diferenciándose del módulo de cupos de granos (v3) por las particularidades del negocio: proveedores especializados, reservas y novedades de carga.

---

## Controladores

| Controlador | Modelo | Propósito |
|-------------|--------|-----------|
| `CupoController` | `CupoFertilizanteErp` | Gestión de cupos de fertilizantes + Excel de carga/arribes |
| `NovedadController` | `NovedadFertilizante` | Novedades operativas de los cupos |
| `ProveedorController` | `ProveedorFertilizante` | Proveedores de fertilizantes |
| `ReservaController` | `ReservaFertilizante` | Reservas de cupos de fertilizantes |

---

## Endpoints especiales (CupoController)

| Acción | Propósito | Formato salida |
|--------|-----------|----------------|
| `actionExcelCarga` | Exportar Excel de cupos de carga | `.xlsx` |
| `actionExcelArribos` | Exportar Excel de arribes de fertilizantes | `.xlsx` |

---

## Rutas (de `rutas.php`)

```php
// Módulo fertilizantes
'fertilizantes/<controller>/<action>' => 'fertilizantes/<controller>/<action>',
```

---

## Integración con ERP

El modelo base `CupoFertilizanteErp` sugiere integración con un sistema ERP externo para sincronización de datos de cupos. Ver [[modulo-erp]].

---

## Notas

> [!info] Generación de Excel
> Los endpoints `actionExcelCarga` y `actionExcelArribos` generan archivos Excel directamente con `GenerarExcel::cargaCuposFertilizante()` y `GenerarExcel::arriboCuposFertilizante()`. Los headers HTTP se configuran manualmente.

> [!warning] Headers CORS manuales
> Los endpoints de descarga de Excel usan `header('Access-Control-Allow-Origin: *')` directamente — esto no pasa por el filtro CORS centralizado. Verificar si es intencional.
