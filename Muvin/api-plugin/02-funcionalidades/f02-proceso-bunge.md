# F-02 — Proceso Bunge: Descarga de Cupos vía HTML

## Descripción

Descarga y procesa las notificaciones de cupos enviadas por **Bunge Argentina** vía correo electrónico. Los cupos llegan en formato **HTML** (en el cuerpo del email o como adjunto HTML).

## Clase principal

`source/components/Plugin/Cupos/ProcesoBunge.php`  
**Parser:** `source/components/Plugin/Cupos/LeerHtmlBungue.php`

## Activación

| Vía | Comando/Endpoint |
|---|---|
| Cron (automático) | `yii lector` → `Ejecutor::correrProcesos()` |
| HTTP manual | `GET /plugin/procesar-bunge` |

## Identificación en BD

- `proceso.id = 3`
- `proceso.nombre = 'Bunge'`
- `proceso.metodo = 'app\components\Plugin\Cupos\ProcesoBunge::DescargarMails'`

## Flujo

```
1. Conectar IMAP con credenciales del ClienteConfiguracion
2. Buscar emails no leídos de Bunge
3. Por cada email:
   a. Extraer HTML del cuerpo o adjunto
   b. LeerHtmlBungue::parsear($html) → array de campos
   c. Crear CupoDocumento (estado = PENDIENTE)
   d. ServiciosMuvin::login() + cargarCupo()
   e. Actualizar estado (PROCESADO / ERROR)
   f. Marcar email como leído
```

## Datos extraídos del HTML

El parser `LeerHtmlBungue` identifica los campos mediante selectores DOM o regex específicos para el formato de Bunge:

| Campo | Descripción |
|---|---|
| Titular | Productor |
| Destinatario | Bunge Argentina |
| Destino | Planta Bunge |
| Grano/Especie | Tipo de grano |
| Cosecha | Campaña |
| Códigos CP | Cartas porte |

## Consideraciones

- El parser HTML es frágil: si Bunge cambia el diseño de su email de notificación, el parser puede fallar silenciosamente o extraer datos incorrectos.
- Se recomienda agregar tests con fixtures de emails reales (anonimizados) para detectar cambios de formato anticipadamente.
