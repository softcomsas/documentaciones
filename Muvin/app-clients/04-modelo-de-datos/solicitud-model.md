# SolicitudModel

> **Archivo fuente:** `lib/src/models/solicitud_model.dart`

## Descripción

Modelo completo de una solicitud de cupo. Se usa en pantallas de detalle y en los BLoCs de gestión de cupos.

## Campos

| Campo | Tipo Dart | Descripción |
|-------|-----------|-------------|
| `id` | `int` | Identificador único |
| `cupoId` | `int` | ID del cupo al que pertenece |
| `demandanteId` | `int` | ID del usuario demandante |
| `demandante` | `String` | Nombre del demandante |
| `cantidad` | `double` | Cantidad solicitada en toneladas |
| `cantidadAsignada` | `double` | Cantidad efectivamente asignada |
| `estado` | `String` | Estado actual (pendiente/aprobada/rechazada) |
| `fechaSolicitud` | `String` | Fecha ISO 8601 |
| `fechaAprobacion` | `String?` | Null si no aprobada |
| `observaciones` | `String?` | Texto libre |
| `cabecera` | `String` | Nombre de cabecera operacional |
| `producto` | `String` | Tipo de grano |

## Constructor / fromJson

```dart
factory SolicitudModel.fromJson(Map<String, dynamic> json) => SolicitudModel(
  id: json['id'],
  cupoId: json['cupo_id'],
  demandanteId: json['demandante_id'],
  demandante: json['demandante'] ?? '',
  cantidad: (json['cantidad'] as num).toDouble(),
  cantidadAsignada: (json['cantidad_asignada'] as num?)?.toDouble() ?? 0.0,
  estado: json['estado'] ?? 'pendiente',
  fechaSolicitud: json['fecha_solicitud'],
  fechaAprobacion: json['fecha_aprobacion'],
  observaciones: json['observaciones'],
  cabecera: json['cabecera'] ?? '',
  producto: json['producto'] ?? '',
);
```

## Notas

- Los campos de fecha llegan como `String` desde la API. No hay parseo a `DateTime`; la lógica de formateo es responsabilidad de la UI.
- `estado` es un string libre, no un enum. Revisar los valores posibles con el equipo de backend.
