# Cron — Programación de Tareas

**Archivo:** `cron/plugin_cupos`

## Configuración del cron

```bash
# Ejecutar el plugin cada 15 minutos
*/15 * * * * cd /app && /usr/local/bin/php yii parametros && /usr/local/bin/php yii lector >> /var/log/cron.log 2>&1

# Limpiar PDFs de más de 30 días (COMENTADO — no activo actualmente)
# 0 3 * * * find /app/pdfs -name '*.pdf' -mtime +30 -exec rm {} \;
```

## Interpretación

| Campo | Valor | Significado |
|---|---|---|
| Minutos | `*/15` | Cada 15 minutos (0, 15, 30, 45) |
| Horas | `*` | Todas las horas |
| Día mes | `*` | Todos los días |
| Mes | `*` | Todos los meses |
| Día semana | `*` | Todos los días de la semana |

## Secuencia de comandos

```
1. cd /app                     → cambiar al directorio de la app
2. php yii parametros          → sincronizar catálogos desde Muvin API
3. && php yii lector           → procesar emails y cargar cupos (solo si paso 2 exitoso)
4. >> /var/log/cron.log 2>&1   → redirigir stdout y stderr al log
```

## Archivo dummy

`cron/plugin_cupos_dummy` contiene un cron vacío o con comandos de prueba. Se usa para entornos de testing donde no se quiere ejecutar el proceso real.

## Monitoreo

```bash
# Ver logs en tiempo real
tail -f /var/log/cron.log

# Ver últimas 100 líneas
tail -n 100 /var/log/cron.log

# Buscar errores
grep -i error /var/log/cron.log

# Ver desde el contenedor Docker
docker exec -it <container> tail -f /var/log/cron.log
```

## Consideraciones operativas

### Concurrencia
No hay mecanismo de lock. Si una ejecución tarda más de 15 minutos, la siguiente se inicia antes de que la anterior termine. Esto puede causar:
- Procesamiento duplicado de emails
- Problemas de concurrencia en la BD

**Solución recomendada:**
```bash
*/15 * * * * cd /app && flock -n /tmp/plugin.lock php yii parametros && flock -n /tmp/plugin.lock php yii lector >> /var/log/cron.log 2>&1
```

### Gestión de logs
El archivo `/var/log/cron.log` crece indefinidamente. Se recomienda configurar `logrotate`:
```
/var/log/cron.log {
    daily
    rotate 30
    compress
    missingok
    notifempty
}
```

### Limpieza de PDFs
La tarea de limpieza de PDFs (`find /app/pdfs -name '*.pdf' -mtime +30 -exec rm {} \;`) está **comentada**. Si los PDFs no se limpian, el disco puede llenarse con el tiempo.
