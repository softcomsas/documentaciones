# SolicitudCardModel

> **Archivo fuente:** `lib/src/models/solicitud_card_model.dart`

## Descripción

Versión reducida de `SolicitudModel` para renderizar en listas (tarjetas). Solo incluye los campos necesarios para la vista de listado, optimizando la deserialización.

## Campos

| Campo | Tipo Dart | Descripción |
|-------|-----------|-------------|
| `id` | `int` | Identificador único |
| `producto` | `String` | Tipo de grano |
| `cantidad` | `double` | Cantidad en toneladas |
| `estado` | `String` | Estado de la solicitud |
| `fecha` | `String` | Fecha de la solicitud |
| `demandante` | `String` | Nombre del demandante |
| `cabecera` | `String?` | Cabecera operacional |

## Uso

Consumido por `SolicitudesPage` y `demanda-cupo/by-demandante` response.

Ver modelo completo: [SolicitudModel](./solicitud-model.md).
