# Modelo: CupoDocumento

**Archivo:** `source/models/Cupos/CupoDocumento.php`  
**Tabla:** `cupo_documento`

## Descripción

Representa un cupo parseado desde un email/adjunto y guardado localmente antes de ser cargado en la Muvin API. Es la entidad central del pipeline de procesamiento de cupos.

## Campos

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | int PK | Identificador único |
| `nombre_archivo` | string | Nombre del archivo adjunto procesado |
| `titular` | string | Productor agropecuario (titular de la carta porte) |
| `rem_com_productor` | string | Remitente comercial productor |
| `rem_com_ventaprimaria` | string | Remitente comercial venta primaria |
| `destinatario` | string | Empresa que recibe el grano (cerealera) |
| `destino` | string | Planta/lugar de descarga |
| `grano_especie` | string | Tipo de grano (soja, maíz, trigo, etc.) |
| `cosecha` | string | Campaña agrícola (ej: 2022/23) |
| `estado` | enum | `PENDIENTE` / `PROCESADO` / `ERROR` |
| `cliente_id` | int FK | Referencia a `cliente.id` |
| `created_at` | timestamp | Fecha de creación |
| `updated_at` | timestamp | Fecha de última actualización |

> Los campos exactos han evolucionado a través de múltiples migraciones ALTER TABLE (2022-07 a 2022-08).

## Ciclo de vida

```
1. Parser extrae datos del adjunto
   └─► CupoDocumento::save() con estado = PENDIENTE

2. CargarMuvin() llama a ServiciosMuvin::cargarCupo()
   ├─► Éxito: estado = PROCESADO
   └─► Error: estado = ERROR (con mensaje de error guardado)
```

## Relaciones

- `belongsTo(Cliente::class, ['cliente_id' => 'id'])`
- `hasMany(CupoCodigo::class, ['cupo_documento_id' => 'id'])`
- `hasMany(CupoMailNotif::class, ['cupo_documento_id' => 'id'])`

## objCupoMuvin — DTO de carga

`source/models/Cupos/objCupoMuvin.php` es un DTO que mapea los campos de `CupoDocumento` al formato esperado por la Muvin API para la operación de carga de cupos.

## CupoCodigo — Códigos de cartas porte

`source/models/Cupos/CupoCodigo.php` (tabla `cupo_codigo`) almacena los códigos alfanuméricos de las cartas porte extraídos durante el parseo. Un `CupoDocumento` puede tener múltiples `CupoCodigo`.
