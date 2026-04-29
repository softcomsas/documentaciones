# F-09 — Sincronización de Catálogos

## Descripción

Mantiene sincronizados los catálogos locales del plugin con los datos maestros de la plataforma Muvin. Se ejecuta antes de cada ciclo de procesamiento para garantizar que los datos de validación estén actualizados.

## Comando

```bash
php yii parametros
```

**Clase:** `source/commands/ParametrosController.php`

## Activación

| Vía | Detalle |
|---|---|
| Cron automático | `*/15 * * * *` → `yii parametros` (antes de `yii lector`) |
| HTTP manual | `GET /plugin/cargar-productos` |
| HTTP manual | `GET /plugin/cargar-destinos` |
| HTTP manual | `GET /plugin/cargar-onccas-productos` |

## Catálogos sincronizados

### 1. Productos / Granos

**Clase:** `Productos::CargarProductos()`  
**Tabla local:** `productos`  
**Fuente:** Muvin API  
**Uso:** Validar y mapear el `grano_especie` del cupo parseado al código de producto que Muvin espera.

### 2. Destinos

**Clase:** `Destinos::CargarDestinos()`  
**Tabla local:** `destinos`  
**Fuente:** Muvin API  
**Uso:** Validar y mapear el `destino` (planta) del cupo al identificador de destino de Muvin.

### 3. ONCCA Destinos-Productos

**Clase:** `OnccaDestinosProductos::sincronizar()`  
**Tabla local:** `oncca_destino_producto`  
**Fuente:** Muvin API  
**Uso:** Validar que la combinación destino+producto sea válida según la normativa ONCCA.

### 4. CUITs de Empresas

**Clase:** `CuitEmpresas::CargarCuitEmpresas()`  
**Tabla local:** `cuit_empresas`  
**Fuente:** Muvin API  
**Uso:** Validar que el CUIT de la cerealera (destinatario del cupo) esté registrado en Muvin.

## Flujo

```
ParametrosController::actionIndex()
    ├─ 1. Productos::CargarProductos()
    │       GET Muvin API → truncate productos → insert nuevos
    ├─ 2. Destinos::CargarDestinos()
    │       GET Muvin API → truncate destinos → insert nuevos
    ├─ 3. OnccaDestinosProductos::sincronizar()
    │       GET Muvin API → actualizar oncca_destino_producto
    └─ 4. CuitEmpresas::CargarCuitEmpresas()
            GET Muvin API → truncate cuit_empresas → insert nuevos
```

## Consideraciones

- **Truncate y recarga**: la estrategia probablemente trunca y recarga las tablas completas. Si la Muvin API falla a mitad, las tablas locales quedan vacías o incompletas, afectando el ciclo de procesamiento siguiente.
- **Frecuencia excesiva**: sincronizar catálogos cada 15 minutos es innecesario para datos que cambian con baja frecuencia. Ver [mejoras-propuestas.md](../08-riesgos-y-deuda-tecnica/mejoras-propuestas.md) MEJ-09.
