# Módulo: BLoCs y State Management

> **Rutas:** `lib/src/blocs/register_bloc.dart`, `lib/src/blocs/phone_code_bloc.dart`, `lib/src/blocs/provider.dart`
> **Criticidad:** 🟡 Media
> **Estado:** Activo

## Propósito

Gestiona el estado reactivo de los formularios de autenticación (login y verificación de código). Usa `RxDart ^0.22.6` con `BehaviorSubject` para streams de validación.

> **Nota:** No usa el paquete `flutter_bloc`. Implementa BLoC manualmente con `InheritedWidget`.

## Arquitectura

```
Provider (InheritedWidget singleton)
├── RegisterBloc     ← Formulario de login (país + teléfono)
└── PhoneCodeBloc    ← Formulario de código SMS
```

## RegisterBloc

```dart
class RegisterBloc {
  BehaviorSubject<String> _countryController = BehaviorSubject<String>();
  BehaviorSubject<String> _phoneController   = BehaviorSubject<String>();

  // Streams de salida con validación
  Stream<String> get countryStream => _countryController.stream.transform(validarCountry);
  Stream<String> get phoneStream   => _phoneController.stream.transform(validarPhone);

  // Stream combinado para habilitar el botón submit
  Stream<bool> get formValidStream =>
      Rx.combineLatest2(countryStream, phoneStream, (c, p) => true);

  // Sinks de entrada
  Function(String) get changeCountry => _countryController.sink.add;
  Function(String) get changePhone   => _phoneController.sink.add;
}
```

### Validadores

| Transform | Regla | Error emitido |
|-----------|-------|---------------|
| `validarCountry` | No vacío | `'Ingrese el código de país'` |
| `validarPhone` | `isNumeric` + length check | `'Número de teléfono no válido'` |

## PhoneCodeBloc

```dart
class PhoneCodeBloc {
  BehaviorSubject<String> _codeController = BehaviorSubject<String>();
  Stream<String> get codeStream => _codeController.stream.transform(validarCodigo);
  Function(String) get changeCode => _codeController.sink.add;
}
```

## Provider (InheritedWidget)

```dart
class Provider extends InheritedWidget {
  static Provider of(BuildContext context) =>
      context.dependOnInheritedWidgetOfExactType<Provider>();

  final bloc = RegisterBloc();
  final phoneBloc = PhoneCodeBloc();
}
```

Se instancia en `main.dart` como root del árbol de widgets. Los BLoCs son singletons por instancia de Provider.

## Riesgos

- ⚠️ `RxDart ^0.22.6` es muy antigua — API de streams cambió significativamente en versiones posteriores.
- ⚠️ Los `BehaviorSubject` nunca se cierran (`dispose()` no llama `close()`) → memory leak potencial.
- ⚠️ El código de país está hardcodeado a `'1'` en `login_page.dart` con `bloc.changeCountry('1')` — el stream de país nunca recibe input del usuario.
- ⚠️ `bloc.dispose()` no está implementado en ningún BLoC.
