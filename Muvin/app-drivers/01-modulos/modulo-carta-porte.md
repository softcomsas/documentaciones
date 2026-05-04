# Módulo: Carta de Porte

> **Ruta:** `lib/src/pages/carta_porte_page.dart`
> **Criticidad:** 🔴 CRÍTICA
> **Estado:** 🚨 ROTO — CRASH AL INICIAR

## Propósito

Permite al chofer fotografiar y subir los remitos inicial y final del viaje. Usa la cámara del dispositivo y envía la imagen codificada en base64 al backend.

## Bug Crítico

```dart
// carta_porte_page.dart
File _image; // declarado como null implícito

// En build():
String base64Image = base64Encode(_image.readAsBytesSync()); // 🔴 NPE aquí
```

`_image` nunca se inicializa antes de que se construya el widget. El método `build()` intenta leer los bytes del archivo sin que el usuario haya tomado ninguna foto. La app **crashea inmediatamente** al navegar a esta ruta.

## Flujo Intencionado (si no estuviera roto)

```
Usuario toca "Carta de Porte" en ViajePagee
  → Abre CartaPortePage
  → Usuario toma foto con cámara (image_picker ^0.6.7)
  → base64Encode(_image.readAsBytesSync())
  → POST chofer-app/subir-remito-inicial  (tipo='carta')
  → POST chofer-app/subir-remito-final    (tipo='descarga')
```

## Acciones de Upload

| Acción | Método | Descripción |
|--------|--------|-------------|
| Subir remito inicial | `subirImagen('carta', archivo)` | Tipo `carta` |
| Subir remito final | `subirImagen('descarga', archivo)` | Tipo `descarga` |

## Fix Requerido

```dart
// Solución mínima
File? _image; // nullable con null safety

// En build():
if (_image == null) {
  return Center(child: Text('Selecciona una imagen primero'));
}
String base64Image = base64Encode(_image!.readAsBytesSync());
```

## Dependencias

- **Depende de:** [[modulo-muvin-provider]]
- **Alcanzable desde:** [[modulo-viaje]]
- **Paquetes:** `image_picker ^0.6.7` (obsoleto), `dart:convert` (base64)

## Riesgos

- 🔴 **NPE inmediato en build()** — la ruta `/cartaporte` no es usable en absoluto.
- 🔴 `image_picker ^0.6.7` es muy antiguo — incompatible con Android 13+.
- ⚠️ `base64Encode` de imagen grande puede bloquear el hilo UI.
