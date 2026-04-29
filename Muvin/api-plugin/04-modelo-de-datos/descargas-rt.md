# Modelo: DescargasRt

**Archivo:** `source/models/DescargasRt.php`  
**Tabla:** `descargas_rt`

## Descripción

Representa una descarga de granos en tiempo real identificada por su COE (Código de Operación Electrónica). Es procesada por el módulo `PluginV2/procedures/DescargaRtProcess.php`.

## Campos

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | int PK | Identificador único |
| `coe` | string | Código de Operación Electrónica (AFIP) |
| `grano` | string | Tipo de grano |
| `cosecha` | string | Campaña agrícola |
| `cuitDepositario` | string | CUIT del depositario (planta receptora) |
| `nroPlanta` | string | Número de planta de descarga |
| `cuitDepositante` | string | CUIT del depositante (productor) |
| `kilos` | decimal | Kilogramos declarados |
| `estado` | enum | `PENDIENTE` / `PROCESADO` / `ERROR` |
| `created_at` | timestamp | Fecha de ingreso al sistema |

> Campos adicionales agregados en migración `m250219_192453_add_new_column_to_descargas_rt.php`.

## Ciclo de vida

```
1. DescargaRtParserFile parsea el archivo adjunto del email RT
   └─► DescargasRt::save() con estado = PENDIENTE

2. DescargaRtProcess llama a ServiciosDescargas
   ├─► Éxito: estado = PROCESADO
   └─► Error: estado = ERROR

3. descargas_rt_notif registra la notificación enviada
```

## Creación en la BD

La tabla fue creada en enero 2025 (`m250131_091817_create_table_rt.php`), indicando que la funcionalidad de DescargasRT es reciente.

## Diferencia con cupo_documento

| Aspecto | cupo_documento | descargas_rt |
|---|---|---|
| Origen | Email IMAP con adjunto PDF/HTML | Email IMAP con archivo RT |
| Identificador | nombre_archivo | COE (AFIP) |
| Procesado por | Plugin/Cupos/Proceso*.php | PluginV2/DescargaRtProcess |
| Módulo | Plugin v1 | Plugin v2 |
| Antigüedad | 2021 | 2025 |
