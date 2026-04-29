# F-07 — Proceso Fyo/Tomas Hnos: Descarga de Cupos vía HTML

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **Tomas Hnos/Fyo** vía correo electrónico en formato **HTML**. Esta es una variante específica del cliente Tomas Hnos para los cupos de Fyo.

## Clase principal

`source/components/Plugin/Cupos/ProcesoFyo.php`  
**Parser:** `source/components/Plugin/Cupos/LeerHtmlTomasHnosFyo.php`

## Activación

| Vía | Comando/Endpoint |
|---|---|
| Cron (automático) | `yii lector` → cliente_conf.id=26 (⚠️ hardcodeado) |
| HTTP manual | `GET /plugin/proceso-fyo` |

## Identificación en BD

- `cliente_configuracion.id = 26` (hardcodeado en `PluginController::actionProcesoFyo()`)
- No tiene `proceso.id` propio en el endpoint; se identifica directamente por configuración

## Flujo

```
1. Obtener ClienteConfiguracion con id=26 (hardcodeado)
2. Conectar IMAP
3. Buscar emails no leídos de Fyo
4. Por cada email:
   a. Extraer HTML
   b. LeerHtmlTomasHnosFyo::parsear($html)
   c. Crear CupoDocumento → cargar en Muvin
   d. Marcar email como leído
```

## Consideraciones

- El ID `26` hardcodeado en el controlador es un punto de fragilidad: si se migra la BD o se hace un restore, el ID puede cambiar. Ver [riesgos-seguridad.md](../08-riesgos-y-deuda-tecnica/riesgos-seguridad.md) SEC-06.
- Tomas Hnos opera con múltiples cerealeras (Cofco, Dreyfus, Fyo), cada una con su propio proceso y cuenta de email.
