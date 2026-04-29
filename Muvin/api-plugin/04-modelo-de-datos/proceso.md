# Modelo: Proceso

**Archivo:** `source/models/Proceso.php`  
**Tabla:** `proceso`

## Descripción

Representa un proceso de descarga/parseo asociado a un cliente. El campo `metodo` contiene el nombre del callable PHP que se ejecutará para ese proceso.

## Campos

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | int PK | Identificador único |
| `nombre` | string | Nombre descriptivo del proceso |
| `activo` | tinyint | 1 = activo, 0 = inactivo |
| `metodo` | string | Callable PHP a ejecutar (ej: `app\components\Plugin\Cupos\ProcesoAca::DescargarMails`) |
| `cliente_id` | int FK | Referencia a `cliente.id` |

## Registros conocidos

| ID | Nombre | Metodo |
|---|---|---|
| 1 | ACA | `app\components\Plugin\Cupos\ProcesoAca::DescargarMails` |
| 2 | Dreyfus | `app\components\Plugin\Cupos\ProcesoDreyfus::DescargarMails` |
| 3 | Bunge | `app\components\Plugin\Cupos\ProcesoBunge::DescargarMails` |
| 4 | SAMSA | `app\components\Plugin\Cupos\ProcesoSamsa::DescargarMails` |
| 5 | Cofco | `app\components\Plugin\Cupos\ProcesoCofco::DescargarMails` |
| 6 | MolinosAgro | `app\components\Plugin\Cupos\ProcesoMolinosAgro::DescargarMails` |

## Riesgo: Dynamic Method Invocation

> 🔴 **Riesgo crítico**: el campo `metodo` se ejecuta directamente:
> ```php
> call_user_func($proc->metodo, $client_conf);
> ```
> Si la base de datos es comprometida, un atacante puede insertar un callable arbitrario y ejecutar código PHP. Ver [riesgos-seguridad.md](../08-riesgos-y-deuda-tecnica/riesgos-seguridad.md).

## Métodos de clase

### `getOne($conditions)`
Retorna un proceso activo por condiciones:
```php
$proceso = Proceso::getOne(['id' => 1, 'activo' => 1]);
```

## Historial de cambios

La tabla `proceso` fue recreada completamente en julio 2022 (`m220701_223309_create_proceso_table.php`). La migración original fue dropeada y rehecha, sugiriendo un cambio de esquema significativo en esa fecha.
