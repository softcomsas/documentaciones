# Entidad: Pedido / Carga

> **Origen:** respuesta `GET chofer-app/pedidos-publicos` y `GET chofer-app/postulaciones`

## Descripción

Representa un pedido de transporte. No existe clase Dart — se usa `dynamic` directamente desde el JSON.

## Shape del objeto

```json
{
  "id": 42,
  "nombreDestino": "Buenos Aires",
  "fechaCarga": "2024-03-15",
  "tipoCarga": "Granel",
  "peso": 20000,
  "distancia": 450,
  "postulado": false
}
```

## Campos

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int | Identificador único del pedido |
| `nombreDestino` | String | Nombre del destino. Si es vacío, se muestra `'XXXXXXXXXX'` |
| `fechaCarga` | String | Fecha estimada de carga (ISO) |
| `tipoCarga` | String | Tipo de mercadería |
| `peso` | num | Peso en kg |
| `distancia` | num | Distancia estimada en km |
| `postulado` | bool | Si el chofer ya se postuló a este pedido |

## Acceso en código

```dart
// cargas_page.dart — sin modelo tipado
final carga = snapshot.data['data'][index];
Text(carga['nombreDestino'] ?? 'XXXXXXXXXX')
```

## Riesgos

- ⚠️ Sin modelo tipado → errores de tipo en runtime no detectables en compile-time.
- ⚠️ Campos opcionales sin manejo de null pueden causar crashes.
