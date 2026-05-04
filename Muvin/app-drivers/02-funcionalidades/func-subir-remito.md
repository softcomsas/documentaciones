# Funcionalidad: Subir Remito / Carta de Porte

## Descripción

El chofer puede fotografiar y subir los comprobantes del viaje: remito inicial (al cargar) y remito final (al entregar). Esta funcionalidad está **actualmente rota** y no puede ser utilizada.

## Estado Actual

> 🔴 **CRASH INMEDIATO** — ver [[modulo-carta-porte]] para detalle del bug.

## Flujo Intencionado

```mermaid
flowchart TD
    A[ViajePagee → Carta de Porte] --> B[CartaPortePage]
    B --> C{_image == null}
    C -->|Sí — estado actual| D[💥 NPE en build]
    C -->|No — tras fix| E[Mostrar imagen seleccionada]
    B --> F[Tap Cámara → image_picker]
    F --> G[_image = File seleccionado]
    G --> H[base64Encode imagen]
    H --> I{Tipo de remito}
    I -->|Inicial| J[subirImagen carta archivo]
    I -->|Final| K[subirImagen descarga archivo]
    J --> L[POST chofer-app/subir-remito-inicial]
    K --> M[POST chofer-app/subir-remito-final]
```

## Payload HTTP (multipart)

```
POST /chofer-app/subir-remito-inicial
Authorization: Bearer {token}
Content-Type: multipart/form-data

archivo: [binary file]
tipo: "carta" | "descarga"
```

## Correcciones Necesarias

1. Inicializar `_image` como `File?` (nullable) con null safety.
2. Mostrar placeholder/instrucción cuando no hay imagen seleccionada.
3. Verificar `_image != null` antes de `readAsBytesSync()`.
4. Actualizar `image_picker` a versión actual (`^1.x`).
5. Procesar encoding en isolate para no bloquear UI.

## Referencias

- [[modulo-carta-porte]]
- [[modulo-viaje]]
- [[modulo-muvin-provider]]
