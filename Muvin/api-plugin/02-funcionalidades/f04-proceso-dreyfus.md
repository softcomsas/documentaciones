# F-04 — Proceso Dreyfus: Descarga de Cupos vía HTML

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **Louis Dreyfus Company** vía correo electrónico. También cubre la variante **Tomas Hnos/Dreyfus**.

## Clase principal

`source/components/Plugin/Cupos/ProcesoDreyfus.php`

## Activación

| Vía | Dreyfus directo | Tomas Hnos/Dreyfus |
|---|---|---|
| HTTP manual | `GET /plugin/procesar-dreyfus` | `GET /plugin/proceso-tomas-hnos-dreyfus` |
| Cron | Via `Ejecutor` proceso.id=2 | cliente_conf.id=24 (⚠️ hardcodeado) |

## Identificación en BD

**Dreyfus directo:**
- `proceso.id = 2`
- `proceso.metodo = 'app\components\Plugin\Cupos\ProcesoDreyfus::DescargarMails'`

**Tomas Hnos/Dreyfus:**
- `cliente_configuracion.id = 24` (hardcodeado en `PluginController`)

## Particularidad: tabla destinos_dreyfus

Dreyfus tiene una tabla específica `destinos_dreyfus` (creada en migración `m220708_151447_destinos_dreyfus_create_table`) para mapear los destinos con el formato de Dreyfus. Esto sugiere que los nombres de destinos de Dreyfus difieren de la nomenclatura estándar de Muvin y requieren una tabla de traducción.

## Flujo

```
1. Conectar IMAP
2. Buscar emails de Dreyfus
3. Por cada email:
   a. Extraer HTML
   b. Parsear campos (regex/DOM)
   c. Mapear destino via destinos_dreyfus
   d. Crear CupoDocumento → cargar en Muvin
   e. Marcar email como leído
```
