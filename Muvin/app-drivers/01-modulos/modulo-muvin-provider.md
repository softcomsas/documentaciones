# Módulo: MuvinProvider

> **Ruta:** `lib/src/providers/muvin_provider.dart`
> **Criticidad:** 🔴 Alta — núcleo de comunicación HTTP
> **Estado:** Activo

## Propósito

Centraliza todas las llamadas REST al backend. Es el único punto de acceso a la API desde la aplicación. Usa el paquete `http ^0.12.1`.

## Configuración

```dart
// config.dart
static const apiUrl = 'https://pruebas.muvinapp.com/api/backend/web/';
// 🔴 URL de pruebas hardcodeada — nunca se configura por entorno
```

El token Bearer se lee desde `Preferences().token` (SharedPreferences) en cada request autenticado.

## Endpoints

| # | Método | Path | Auth | Descripción |
|---|--------|------|------|-------------|
| 1 | POST | `login/send-code` | ❌ | Solicita envío de SMS con código |
| 2 | POST | `login/telefono` | ❌ | Valida código SMS → retorna token |
| 3 | GET | `pais/select` | ❌ | Lista de países (llamada no usada visiblemente) |
| 4 | GET | `chofer-app/pedidos-publicos` | ✅ Bearer | Cargas/pedidos disponibles |
| 5 | GET | `chofer-app/postulaciones` | ✅ Bearer | Postulaciones del chofer |
| 6 | GET | `chofer-app/mi-viaje` | ✅ Bearer | Viaje asignado activo |
| 7 | POST | `chofer-app/postularme?id_pedido=X` | ✅ Bearer | Postularse a un pedido |
| 8 | POST | `chofer-app/des-postularme?id_pedido=X` | ✅ Bearer | Cancelar postulación |
| 9 | POST | `chofer-app/actualizar-posicion` | ✅ Bearer | Enviar latitud/longitud GPS |
| 10 | POST | `chofer-app/subir-remito-inicial` | ✅ Bearer | Upload remito (multipart) |
| 11 | POST | `chofer-app/subir-remito-final` | ✅ Bearer | Upload remito final (multipart) |

## Manejo de Errores

```dart
dynamic manejadorErroresResp(http.Response response) {
  switch (response.statusCode) {
    case 422: return jsonDecode(response.body); // Validation error
    default:  return jsonDecode(response.body);
  }
  // No lanza excepciones — el caller debe interpretar la respuesta
}
```

> ⚠️ Todos los status code devuelven el mismo comportamiento — no hay distinción entre 200, 401, 500.

## Upload de Imágenes

`subirImagen(String tipo, String archivo)` usa `http.MultipartRequest`:

```dart
var request = http.MultipartRequest(
  'POST',
  Uri.parse(Config.apiUrl + 'chofer-app/subir-remito-inicial')
);
request.headers['Authorization'] = 'Bearer ' + Preferences().token;
request.files.add(await http.MultipartFile.fromPath('archivo', archivo));
request.fields['tipo'] = tipo; // 'carta' | 'descarga'
```

## Riesgos

- 🔴 URL `pruebas.muvinapp.com` hardcodeada — no hay configuración por entorno.
- ⚠️ No hay timeout configurado en ningún request HTTP.
- ⚠️ Paquete `http ^0.12.1` es obsoleto — versión actual es 1.x.
- ⚠️ `manejadorErroresResp` no diferencia errores — `401` y `200` producen el mismo resultado.
- ⚠️ `obtenerPaises()` existe en el provider pero el selector de país está comentado en la UI.
