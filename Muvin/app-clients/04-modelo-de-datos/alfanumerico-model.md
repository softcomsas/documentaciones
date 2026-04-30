# AlfanumericoModel

> **Archivo fuente:** `lib/src/models/alfanumerico_model.dart`

## Descripción

Modelo genérico para ítems de catálogos alfanuméricos. Usado en dropdowns y selects de filtros (productos, destinos, tipos, etc.).

## Campos

| Campo | Tipo Dart | Descripción |
|-------|-----------|-------------|
| `id` | `int` | Identificador |
| `codigo` | `String` | Código corto del ítem |
| `descripcion` | `String` | Texto descriptivo visible al usuario |
| `tipo` | `String` | Categoría del catálogo al que pertenece |

## Endpoint que lo provee

`GET alfanumerico?tipo=X` → Ver [catalogos-endpoints](../03-servicios-backend/catalogos-endpoints.md).
