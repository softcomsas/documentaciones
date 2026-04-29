# Inventario de Comandos de Consola

**Ruta base:** `source/commands/`  
**Entry point:** `php yii <comando>`

## Comandos de producción

| Comando | Clase | Método | Descripción |
|---|---|---|---|
| `yii lector` | `LectorController` | `actionIndex` | Ejecuta el ciclo completo de descarga y procesamiento de cupos |
| `yii parametros` | `ParametrosController` | `actionIndex` | Sincroniza catálogos desde Muvin API |

## Comandos auxiliares

| Comando | Clase | Descripción |
|---|---|---|
| `yii claves` | `ClavesController` | Gestión de claves/credenciales |
| `yii hello` | `HelloController` | Comando de bienvenida/prueba |
| `yii test` | `TestController` | Pruebas manuales (no usar en producción) |
| `yii test-excel` | `TestExcelController` | Pruebas del parser de Excel |

## Secuencia de ejecución en producción

```bash
# 1. Sincronizar catálogos (siempre antes del lector)
php yii parametros

# 2. Procesar emails y cargar cupos
php yii lector
```

## Logs

La salida de los comandos se redirige a `/var/log/cron.log`:

```
*/15 * * * * cd /app && php yii parametros && php yii lector >> /var/log/cron.log 2>&1
```

Para ver los logs en tiempo real:
```bash
tail -f /var/log/cron.log
```
