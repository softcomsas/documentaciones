# F-06 — Proceso MolinosAgro: Descarga de Cupos vía HTML

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **Molinos Agro** vía correo electrónico en formato **HTML**.

## Clase principal

`source/components/Plugin/Cupos/ProcesoMolinosAgro.php`  
**Parser:** `source/components/Plugin/Cupos/LeerHtmlMolinosAgro.php`

## Activación

| Vía | Comando/Endpoint |
|---|---|
| Cron (automático) | `yii lector` → proceso.id=6 |
| HTTP manual | `GET /plugin/procesar-molinos-agro` |

## Identificación en BD

- `proceso.id = 6`
- `proceso.metodo = 'app\components\Plugin\Cupos\ProcesoMolinosAgro::DescargarMails'`

## Flujo

```
1. Conectar IMAP
2. Buscar emails no leídos de Molinos Agro
3. Por cada email:
   a. Extraer HTML del email
   b. LeerHtmlMolinosAgro::parsear($html) → datos del cupo
   c. Crear CupoDocumento (estado = PENDIENTE)
   d. ServiciosMuvin::login() + cargarCupo()
   e. Actualizar estado
   f. Marcar email como leído
```
