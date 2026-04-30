# Inventario de Seguridad — app-clients

> [[README]] · [[08-riesgos-y-deuda-tecnica/hotspots]]

## Resumen ejecutivo

| Severidad | Cantidad |
|-----------|---------|
| 🔴 Crítico | 4 |
| 🟠 Alto | 3 |
| ⚠️ Medio | 4 |

---

## Hallazgos Críticos

### SEC-01 · X-Api-Key hardcodeada en código fuente
- **Archivo:** `lib/src/config/config.dart`
- **Valor expuesto:** `uH9hatw8G3lQls7fE3`
- **Impacto:** Cualquier persona con acceso al repositorio (o al APK descompilado) puede realizar llamadas a la API como si fuera la app. Permite abusar de todos los endpoints sin autenticación de usuario.
- **Mitigación:** Usar variables de entorno en tiempo de build con `--dart-define` o `flutter_dotenv`. Rotar la clave inmediatamente si fue expuesta en un repositorio público.

### SEC-02 · FCM Server Token hardcodeado
- **Archivo:** `lib/src/config/config.dart`
- **Valor expuesto:** `Config.serverToken`
- **Impacto:** Permite a cualquiera enviar notificaciones push a **todos los dispositivos registrados** en la app.
- **Mitigación:** El server token nunca debe estar en el cliente. Las notificaciones deben enviarse exclusivamente desde el backend.

### SEC-03 · Captcha hardcodeado en login
- **Archivo:** `lib/src/providers/muvin_provider.dart` → `login()`
- **Impacto:** La protección anti-bot del endpoint de login es nula. Un atacante puede automatizar intentos de login sin resolver ningún captcha.
- **Mitigación:** Implementar captcha dinámico (reCAPTCHA v3 o similar) o eliminar el campo si el backend no lo valida realmente.

### SEC-04 · access_token almacenado sin cifrado
- **Archivo:** `lib/src/config/preference.dart`
- **Impacto:** En dispositivos rooteados/jailbreakeados, el token JWT es legible directamente desde `SharedPreferences`. Permite secuestro de sesión.
- **Mitigación:** Usar `flutter_secure_storage` para tokens sensibles.

---

## Hallazgos Altos

### SEC-05 · No hay refresh de token
- **Impacto:** Token expirado = sesión rota silenciosa. El usuario no recibe error claro; los requests simplemente fallan con 401.
- **Mitigación:** Implementar interceptor HTTP que detecte 401 y use el `refresh_token` para obtener un nuevo `access_token`.

### SEC-06 · No hay invalidación de token en logout
- **Impacto:** El token sigue siendo válido en el servidor tras el logout. Un atacante que obtenga el token antes del logout puede usarlo hasta su expiración natural.
- **Mitigación:** Implementar endpoint `POST /logout` en el backend que invalide el token.

### SEC-07 · URL de WebSocket sin autenticación explícita
- **Archivo:** `lib/main.dart` (conexión socket)
- **Impacto:** La conexión a `socket.muvinapp.com` puede no estar verificando la identidad del cliente. Posible escucha de eventos de otros usuarios.
- **Mitigación:** Incluir el `access_token` en el handshake del socket (`auth: {token: ...}`).

---

## Hallazgos Medios

### SEC-08 · Campos de formulario sin sanitización explícita
- Los campos de texto (`patente`, `chofer`, etc.) se envían directamente al backend sin limpieza. El backend debe sanitizar.

### SEC-09 · Sin certificate pinning
- La app no implementa SSL pinning. Sujeta a ataques MITM en redes no confiables.

### SEC-10 · `refresh_token` guardado pero no usado
- Se guarda en SharedPreferences (sin cifrado) pero no tiene ningún uso en la app. Dato sensible almacenado innecesariamente.

### SEC-11 · Sin ofuscación de código en builds de release
- No se encontró configuración de ProGuard/R8 ni `--obfuscate` en los scripts de build. El APK puede descompilarse fácilmente y exponer las credenciales hardcodeadas (SEC-01, SEC-02).
