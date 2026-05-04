# Entidad: Preferencias (SharedPreferences)

> **Ruta:** `lib/src/share/preference.dart`

## Descripción

Singleton que encapsula el acceso a `SharedPreferences`. Almacena el estado persistente local del usuario.

## Campos almacenados

| Clave | Tipo | Descripción | Getter/Setter |
|-------|------|-------------|---------------|
| `token` | String | Token Bearer de sesión | `get token` / `set token` |
| `ultimaPagina` | String | Última ruta visitada | `get ultimaPagina` / `set ultimaPagina` |

## Implementación

```dart
class Preferences {
  static final Preferences _instance = Preferences._internal();
  factory Preferences() => _instance;
  Preferences._internal();

  static SharedPreferences _prefs;

  static Future init() async {
    _prefs = await SharedPreferences.getInstance();
  }

  String get token => _prefs.getString('token') ?? '';
  set token(String value) => _prefs.setString('token', value);

  String get ultimaPagina => _prefs.getString('ultimaPagina') ?? '/home';
  set ultimaPagina(String value) => _prefs.setString('ultimaPagina', value);
}
```

## Riesgos

- 🔴 El token se guarda como **texto plano** — sin cifrado, legible en el dispositivo.
- ⚠️ `ultimaPagina` no se usa actualmente para restaurar navegación.
- ⚠️ No hay mecanismo de logout que limpie las preferencias.
