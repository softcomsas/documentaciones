# F-01 — Proceso ACA: Descarga de Cupos vía PDF

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **ACA (Asociación de Cooperativas Argentinas)** vía correo electrónico. Los cupos llegan como adjuntos en formato **PDF**, que son parseados para extraer los datos de la carta porte.

## Clase principal

`source/components/Plugin/Cupos/ProcesoAca.php`

## Activación

| Vía | Comando/Endpoint |
|---|---|
| Cron (automático) | `yii lector` → `Ejecutor::correrProcesos()` |
| HTTP manual | `GET /plugin/proceso-aca` |

## Identificación en BD

- `proceso.id = 1`
- `proceso.nombre = 'ACA'`
- `proceso.metodo = 'app\components\Plugin\Cupos\ProcesoAca::DescargarMails'`

## Flujo

```
1. Conectar al servidor IMAP del cliente (ClienteConfiguracion.srvmail)
2. Buscar emails no leídos con adjuntos PDF
3. Por cada email:
   a. Descargar el PDF adjunto → guardar en /app/pdfs/
   b. LeerPdf::parsear($rutaPdf) → extraer campos de la carta porte
   c. Crear CupoDocumento (estado = PENDIENTE)
   d. Obtener credenciales Muvin del Cliente (desencriptar AES)
   e. ServiciosMuvin::login()
   f. ServiciosMuvin::cargarCupo(new objCupoMuvin($cupoDoc))
   g. Actualizar CupoDocumento:
      - estado = PROCESADO (si OK)
      - estado = ERROR + mensaje (si falla)
   h. Marcar email como leído en IMAP
4. Retornar resumen de resultados
```

## Datos extraídos del PDF

| Campo | Descripción |
|---|---|
| Titular | Productor agropecuario |
| Rem. com. productor | Remitente comercial productor |
| Rem. com. venta primaria | Remitente comercial de venta primaria |
| Destinatario | ACA |
| Destino | Planta de descarga |
| Grano/Especie | Tipo de grano |
| Cosecha | Campaña agrícola |
| Códigos CP | Códigos de carta porte (guardados en `cupo_codigo`) |

## Parser

`LeerPdf.php` extrae el texto del PDF y aplica regex o extracción posicional para identificar cada campo.

## Consideraciones

- Los PDFs se guardan físicamente en el servidor (directorio `/app/pdfs/`). La tarea de limpieza automática (`find /app/pdfs -name '*.pdf' -mtime +30`) está **comentada** en el cron.
- Si el PDF no se puede parsear, el `CupoDocumento` queda en estado `ERROR` con el mensaje de excepción.
