# Flujo: Arranque y Autenticación

## Diagrama Completo

```mermaid
flowchart TD
    A([App Start]) --> B[main.dart: Preferences.initPrefs]
    B --> C[SplashScreen — 3 segundos]
    C --> D{Verificar permisos}
    D -->|Denegados| E[Solicitar permisos\nlocalización + cámara]
    D -->|Concedidos| F{¿token en SharedPrefs?}
    E --> F
    F -->|Sí| G[Navigator → /home]
    F -->|No| H[Navigator → /login]

    H --> I[LoginPage]
    I --> J[Usuario ingresa número]
    J --> K[POST login/send-code]
    K -->|Éxito| L[Navigator → /phoneVerification]
    K -->|Error| M[mostrarAlerta error]

    L --> N[PhoneVerificationPage]
    N --> O[Usuario ingresa código SMS]
    O --> P[POST login/telefono]
    P -->|Éxito| Q[Preferences.token = token]
    Q --> R[Navigator.pushReplacement → /home]
    P -->|Error| S[mostrarAlerta código incorrecto]

    G --> T[HomePage]
    R --> T
```

## Notas

- No existe flujo de logout — el usuario no puede cerrar sesión desde la UI.
- El token no tiene fecha de expiración visible — si expira, el usuario verá errores 401 sin redirección automática.
- Los permisos se solicitan en SplashScreen sin explicación al usuario.
