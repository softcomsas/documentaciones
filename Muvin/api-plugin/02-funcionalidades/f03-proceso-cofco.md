# F-03 — Proceso Cofco: Descarga de Cupos vía HTML

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **Cofco International** vía correo electrónico. Los cupos llegan en formato **HTML**. Este mismo proceso es reutilizado para la variante **Tomas Hnos/Cofco**.

## Clase principal

`source/components/Plugin/Cupos/ProcesoCofco.php`  
**Parser:** `source/components/Plugin/Cupos/LeerHtmlCofco.php`

## Activación

| Vía | Cofco directo | Tomas Hnos/Cofco |
|---|---|---|
| HTTP manual | `GET /plugin/procesar-cofco` | `GET /plugin/procesar-cofco-tomas-hnos` |
| Cron | Via `Ejecutor` con proceso.id=5 | Via `Ejecutor` con cliente_conf.id=20 (⚠️ hardcodeado) |

## Identificación en BD

**Cofco directo:**
- `proceso.id = 5`
- `proceso.metodo = 'app\components\Plugin\Cupos\ProcesoCofco::DescargarMails'`

**Tomas Hnos/Cofco:**
- `cliente_configuracion.id = 20` (hardcodeado en `PluginController`)

## Flujo

```
1. Conectar IMAP
2. Buscar emails no leídos de Cofco
3. Por cada email:
   a. Extraer HTML
   b. LeerHtmlCofco::parsear($html)
   c. Crear CupoDocumento → cargar en Muvin
   d. Marcar email como leído
```

## Variante Tomas Hnos/Cofco

Tomas Hnos recibe cupos de Cofco en una cuenta de correo diferente. El proceso de parsing es idéntico (`ProcesoCofco`), pero las credenciales IMAP son las de la cuenta de Tomas Hnos. La configuración IMAP correspondiente tiene `id = 20` en la tabla `cliente_configuracion`.

## Consideraciones

- El ID hardcodeado `20` en `PluginController::actionProcesarCofcoTomasHnos()` es un riesgo operativo: si el registro cambia de ID, el endpoint fallará sin aviso. Ver [riesgos-seguridad.md](../08-riesgos-y-deuda-tecnica/riesgos-seguridad.md).
