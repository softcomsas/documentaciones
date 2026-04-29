# F-05 — Proceso SAMSA: Descarga de Cupos vía HTML

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **SAMSA** vía correo electrónico en formato **HTML**.

## Clase principal

`source/components/Plugin/Cupos/ProcesoSamsa.php`  
**Parser:** `source/components/Plugin/Cupos/LeerHtmlSamsa.php`

## Activación

| Vía | Comando/Endpoint |
|---|---|
| Cron (automático) | `yii lector` → proceso.id=4 |
| HTTP manual | `GET /plugin/procesar-samsa` |

## Identificación en BD

- `proceso.id = 4`
- `proceso.metodo = 'app\components\Plugin\Cupos\ProcesoSamsa::DescargarMails'`

## Flujo

```
1. Conectar IMAP
2. Buscar emails no leídos de SAMSA
3. Por cada email:
   a. Extraer HTML del email
   b. LeerHtmlSamsa::parsear($html) → datos del cupo
   c. Crear CupoDocumento (estado = PENDIENTE)
   d. ServiciosMuvin::login() + cargarCupo()
   e. Actualizar estado
   f. Marcar email como leído
```

## Datos extraídos

| Campo | Descripción |
|---|---|
| Titular | Productor |
| Destinatario | SAMSA |
| Destino | Planta SAMSA |
| Grano/Especie | Tipo de grano |
| Cosecha | Campaña |
| Códigos CP | Cartas porte asociadas |
