# Inventario de Endpoints HTTP

**Controlador:** `PluginController`  
**Base:** `/plugin`

> ⚠️ **Sin autenticación**: todos los endpoints son públicos.

## Tabla completa

| Método | Ruta | Acción | Proceso | ID lookup |
|---|---|---|---|---|
| GET | `/plugin` | `actionIndex` | Todos los clientes activos | Dinámico (BD) |
| GET | `/plugin/proceso-aca` | `actionProcesoAca` | ProcesoAca | proceso.id=1 |
| GET | `/plugin/procesar-bunge` | `actionProcesarBunge` | ProcesoBunge | proceso.id=3 |
| GET | `/plugin/procesar-samsa` | `actionProcesarSamsa` | ProcesoSamsa | proceso.id=4 |
| GET | `/plugin/procesar-cofco` | `actionProcesarCofco` | ProcesoCofco | proceso.id=5 |
| GET | `/plugin/procesar-molinos-agro` | `actionProcesarMolinosAgro` | ProcesoMolinosAgro | proceso.id=6 |
| GET | `/plugin/procesar-dreyfus` | `actionProcesarDreyfus` | ProcesoDreyfus | proceso.id=2 |
| GET | `/plugin/procesar-cofco-tomas-hnos` | `actionProcesarCofcoTomasHnos` | ProcesoCofco | ⚠️ cliente_conf.id=20 |
| GET | `/plugin/proceso-tomas-hnos-dreyfus` | `actionProcesoTomasHnosDreyfus` | ProcesoDreyfus | ⚠️ cliente_conf.id=24 |
| GET | `/plugin/proceso-fyo` | `actionProcesoFyo` | ProcesoFyo | ⚠️ cliente_conf.id=26 |
| GET | `/plugin/cargar-productos` | `actionCargarProductos` | Sync productos | — |
| GET | `/plugin/cargar-destinos` | `actionCargarDestinos` | Sync destinos | — |
| GET | `/plugin/cargar-onccas-productos` | `actionCargarOnccasProductos` | Sync ONCCA | — |

## Formato de respuesta

Todos los endpoints retornan JSON:

```json
// Éxito
[
  { "empresa": "ACA", "procesados": 3, "errores": 0 },
  ...
]

// Error
"No se encontró el proceso"  // HTTP 404
```

## Documentación Swagger

Los endpoints están documentados con anotaciones `@SWG`. El spec Swagger se genera y sirve en `/swagger`.
