# Stack Tecnológico — app-drivers

> [[vision-general]]

## Core

| Tecnología | Versión | Propósito | Estado vendor |
|------------|---------|-----------|---------------|
| Flutter | 1.x (inferido por SDK constraint) | Framework UI cross-platform | ⚠️ EOL — Flutter 1.x fue reemplazado por 2.x con null safety |
| Dart SDK | >=2.1.0 <3.0.0 | Lenguaje — **sin null safety** | ⚠️ Obsoleto — Dart 3.x ya es la versión estable |

## Dependencias de producción

| Paquete | Versión | Propósito | Estado |
|---------|---------|-----------|--------|
| `cupertino_icons` | ^0.1.2 | Íconos iOS | ⚠️ Muy antiguo (actual: ^1.x) |
| `pin_code_text_field` | ^1.4.0 | Campo de código PIN (verificación SMS) | ⚠️ Deprecado — reemplazado por `pin_code_fields` |
| `http` | 0.12.0+1 | Cliente HTTP para REST | ⚠️ Antiguo (actual: ^1.x) |
| `flutter_swiper` | ^1.1.6 | Carrusel de tarjetas (cargas/postulaciones) | ⚠️ Abandonado |
| `image_picker` | ^0.6.1+4 | Captura/selección de fotos | ⚠️ Muy antiguo (actual: ^1.x) |
| `shared_preferences` | cualquiera | Almacenamiento clave-valor local (token) | ✅ Vigente |
| `modal_progress_hud` | ^0.1.3 | Loading overlay | ⚠️ Obsoleto |
| `rxdart` | ^0.22.6 | BLoC: Streams reactivos | ⚠️ Antiguo (actual: ^0.27.x) |
| `permission_handler` | ^4.0.0 | Permisos de ubicación y cámara | ⚠️ API rota en versiones modernas |
| `path_provider` | cualquiera | Rutas de sistema de archivos | ✅ Vigente |
| `flutter_map` | ^0.8.2 | Mapas (importado pero no usado en pages visibles) | ⚠️ Antiguo |
| `geolocator` | ^5.1.3 | GPS — posición actual | ⚠️ API obsoleta (actual: ^13.x) |
| `mime` | ^0.9.6+3 | Detección de MIME type para upload de imágenes | ✅ Vigente |

## Dependencias de desarrollo

| Paquete | Versión | Propósito |
|---------|---------|-----------|
| `flutter_test` | SDK | Tests de widgets (solo test por defecto) |
| `flutter_launcher_icons` | dev | Generación de íconos de launcher |

## Configuración crítica

| Parámetro | Valor | Riesgo |
|-----------|-------|--------|
| `apiUrl` | `https://pruebas.muvinapp.com/api/backend/web/` | 🔴 URL de **pruebas** en código de producción |
| `tokenMapBox` | `pk.eyJ1Ijoi...` (hardcodeado) | 🔴 Token de producción expuesto en repo |
| `apiKey` / `language` / `region` | `'api_key'`, `'vi'`, `'VN'` | ⚠️ Valores de Vietnam — sin uso visible |
