# Flujo: Sincronización de Catálogos

## Descripción

Proceso que mantiene actualizados los catálogos locales (productos, destinos, CUITs, ONCCA) sincronizándolos desde la Muvin API. Se ejecuta al inicio de cada ciclo del cron (`yii parametros`).

## Diagrama

```
cron: php yii parametros
    │
    ▼
ParametrosController::actionIndex()
    │
    ├─ 1. Productos::CargarProductos()
    │         GET Muvin API /productos
    │         → Truncar tabla local `productos`
    │         → Insertar nuevos registros
    │
    ├─ 2. Destinos::CargarDestinos()
    │         GET Muvin API /destinos
    │         → Truncar tabla local `destinos`
    │         → Insertar nuevos registros
    │
    ├─ 3. OnccaDestinosProductos::sincronizar()
    │         GET Muvin API /oncca-destinos-productos (o similar)
    │         → Actualizar tabla `oncca_destino_producto`
    │
    └─ 4. CuitEmpresas::CargarCuitEmpresas()
              GET Muvin API /cuit-empresas
              → Truncar tabla local `cuit_empresas`
              → Insertar nuevos registros
```

## Propósito de cada catálogo

| Catálogo | Uso en el flujo de cupos |
|---|---|
| `productos` | Validar/mapear el grano_especie del cupo contra los productos aceptados por Muvin |
| `destinos` | Validar/mapear el destino del cupo contra los destinos conocidos en Muvin |
| `cuit_empresas` | Validar que el CUIT de la cerealera esté registrado en Muvin |
| `oncca_destino_producto` | Validar combinaciones válidas de destino+producto según ONCCA |

## También disponible vía HTTP

Los mismos catálogos pueden sincronizarse manualmente mediante endpoints HTTP:

```
GET /plugin/cargar-productos
GET /plugin/cargar-destinos
GET /plugin/cargar-onccas-productos
```

## Consideraciones

- **Orden importa**: Los catálogos deben estar sincronizados antes de procesar cupos. Por eso el cron ejecuta `yii parametros` primero.
- **Truncate**: La estrategia de sincronización probablemente trunca y recarga las tablas (no es un merge incremental), lo que puede causar pérdida de datos si la Muvin API no responde correctamente.
- **Frecuencia**: Cada 15 minutos puede ser excesivo para catálogos que cambian con poca frecuencia. Una mejora sería sincronizar catálogos menos frecuentemente (ej: 1 vez por día).
