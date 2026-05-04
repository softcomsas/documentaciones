# Riesgos y Deuda Técnica

> **Sección:** 08-riesgos-y-deuda-tecnica
> **Proyecto:** app-drivers

## Hotspots Críticos

### 🔴 HOT-001: CartaPortePage — NPE inmediato

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `lib/src/pages/carta_porte_page.dart` |
| **Bug** | `File _image` nunca inicializado → `_image.readAsBytesSync()` en `build()` lanza NPE |
| **Impacto** | La ruta `/cartaporte` **nunca puede abrirse** — crash garantizado |
| **Severidad** | 🔴 Crítica |
| **Fix** | Declarar `File? _image` (nullable), verificar null antes de acceder, mostrar placeholder |

---

### 🔴 HOT-002: Timer GPS sin cancelar

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `lib/src/pages/home_page.dart` |
| **Bug** | `Timer.periodic` en `initState` nunca cancelado en `dispose()` |
| **Impacto** | Memory leak + requests HTTP infinitos + batería drenada |
| **Severidad** | 🔴 Alta |
| **Fix** | Guardar referencia al timer, cancelar en `dispose()` |

---

### 🔴 HOT-003: URL de pruebas en producción

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `lib/src/config/config.dart` |
| **Bug** | `apiUrl = 'https://pruebas.muvinapp.com/api/backend/web/'` hardcodeada |
| **Impacto** | La app en producción apunta al entorno de pruebas |
| **Severidad** | 🔴 Crítica |
| **Fix** | Variables de entorno con `--dart-define` o `flutter_dotenv` |

---

### 🔴 HOT-004: Token MapBox expuesto en repositorio

| Atributo | Detalle |
|----------|---------|
| **Archivo** | `lib/src/config/config.dart` |
| **Bug** | Token privado de MapBox commiteado en el repo |
| **Impacto** | Uso malicioso del token → cargos en la cuenta de MapBox |
| **Severidad** | 🔴 Crítica |
| **Acción inmediata** | **Rotar el token en el dashboard de MapBox AHORA** |

---

## Deuda Técnica

| # | Item | Área | Esfuerzo |
|---|------|------|----------|
| DT-01 | Migrar a Flutter 3.x + null safety | Todo el proyecto | Alto |
| DT-02 | Actualizar todas las dependencias | pubspec.yaml | Alto |
| DT-03 | Implementar sistema de entornos | config.dart | Bajo |
| DT-04 | Cifrar token con `flutter_secure_storage` | preference.dart | Bajo |
| DT-05 | Crear modelos Dart tipados para JSON | pages/*.dart | Medio |
| DT-06 | Eliminar `signup_page.dart` (dead code) | pages/ | Trivial |
| DT-07 | Implementar auth guard para 401 | muvin_provider.dart | Bajo |
| DT-08 | Añadir timeout a requests HTTP | muvin_provider.dart | Bajo |
| DT-09 | Habilitar selector de país en login | login_page.dart | Bajo |
| DT-10 | Reemplazar `flutter_swiper` abandonado | cargas_page.dart | Medio |
| DT-11 | Eliminar valores `language='vi'`, `region='VN'` | config.dart | Trivial |
| DT-12 | Implementar `dispose()` en BLoCs | blocs/*.dart | Bajo |
| DT-13 | Pull-to-refresh en Cargas y Postulaciones | cargas_page.dart, postulaciones_page.dart | Bajo |
| DT-14 | Manejo diferenciado de status HTTP en provider | muvin_provider.dart | Medio |
| DT-15 | Certificate pinning para URL de producción | muvin_provider.dart | Medio |

---

## Recomendaciones Prioritarias

### Corto plazo (antes de próximo release)

1. **Rotar token MapBox** inmediatamente.
2. **Fix CartaPortePage** — inicializar `_image` como nullable.
3. **Cancelar Timer GPS** en `dispose()` de `HomePage`.
4. **Cambiar `apiUrl`** a la URL de producción correcta.

### Mediano plazo

5. Migrar a Flutter 3+ con null safety (requiere actualizar APIs de geolocator, image_picker, etc.).
6. Implementar gestión de entornos (dev/staging/prod).
7. Crear modelos de datos tipados.

### Largo plazo

8. Reescritura parcial o total para adoptar arquitectura moderna (Riverpod/Bloc 8.x, go_router, etc.).
9. Tests unitarios y de widgets (cobertura actual: 0%).
10. CI/CD con Flutter + Firebase App Distribution.
