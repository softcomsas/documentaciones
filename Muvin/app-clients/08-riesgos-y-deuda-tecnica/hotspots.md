# Hotspots de Complejidad — app-clients

> [[README]] · [[deuda-tecnica]] · [[security-inventory]]

## Hotspots identificados

### H-01 · `MuvinProvider` — Dios de la HTTP
- **Archivo:** `lib/src/providers/muvin_provider.dart` (~515 líneas)
- **Problema:** Un único archivo concentra los ~20 métodos HTTP de toda la app. Alta cohesión negativa: cualquier cambio en la API requiere tocar este archivo.
- **Complejidad:** Alta — sin separación por dominio (cupos, cargas, auth).
- **Acción recomendada:** Dividir en `CuposApiService`, `CargasApiService`, `AuthApiService`.

### H-02 · `config.dart` — Credenciales hardcodeadas
- **Archivo:** `lib/src/config/config.dart`
- **Problema:** 4 credenciales críticas en código fuente. Ver [SEC-01 a SEC-04](../05-inventarios/security-inventory.md).
- **Complejidad:** Baja de cambiar — impacto de seguridad CRÍTICO.
- **Acción recomendada:** Migrar a `--dart-define` / `flutter_dotenv`. **Prioridad 1.**

### H-03 · BLoCs sin dispose garantizado
- **Archivos:** todos los `*_bloc.dart`
- **Problema:** Si un `StatefulWidget` no llama a `bloc.dispose()` en su `dispose()`, los `BehaviorSubject` quedan abiertos → memory leak progresivo.
- **Complejidad:** Media — requiere auditar todos los widgets que usan BLoCs.

### H-04 · Flujo de cargas sin transaccionalidad
- **Archivos:** `pedido_bloc.dart`, `carga_nuevo_pedido_page.dart`
- **Problema:** `POST pedido` + `PUT cupo/:id` son dos llamadas HTTP sin transacción. El fallo de la segunda deja datos inconsistentes.
- **Complejidad:** Alta — requiere lógica de compensación o rediseño de API.

### H-05 · Dependencias legacy bloquean actualización de Flutter
- **Archivos:** `pubspec.yaml`
- **Problema:** 6 paquetes con versiones incompatibles con Flutter 3.x / Dart 3.x (null-safety). Impide beneficiarse de mejoras de rendimiento y seguridad del SDK.
- **Complejidad:** Muy alta — requiere migration a null-safety en todo el codebase.

### H-06 · Token refresh no implementado
- **Archivos:** `muvin_provider.dart`, `preference.dart`
- **Problema:** El `refresh_token` se guarda pero nunca se usa. Sesiones expiradas producen errores silenciosos.
- **Complejidad:** Media — añadir interceptor HTTP.
