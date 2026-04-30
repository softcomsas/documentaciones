# F-03 · Aceptación de Términos y Condiciones

> **Módulo:** [modulo-auth](../01-modulos/modulo-auth.md)
> **Ruta:** `/terminos`

## Descripción

Pantalla presentada en el primer ingreso. Muestra el texto de Términos y Condiciones y requiere que el usuario los acepte explícitamente para poder continuar a `/home`. Si el usuario ya los aceptó (flag en `SharedPreferences`), la pantalla se omite.

## Flujo

```mermaid
sequenceDiagram
    participant LoginBloc
    participant Navigator
    participant TerminosPage
    participant Preference

    LoginBloc->>Preference: getTerminosAceptados()
    alt ya aceptados
        LoginBloc->>Navigator: pushReplacementNamed('/home')
    else no aceptados
        LoginBloc->>Navigator: pushReplacementNamed('/terminos')
        TerminosPage-->>Usuario: Mostrar texto T&C
        Usuario->>TerminosPage: tap "Acepto"
        TerminosPage->>Preference: setTerminosAceptados(true)
        TerminosPage->>Navigator: pushReplacementNamed('/home')
    end
```

## Riesgos

- ⚠️ Si se actualizan los T&C en el backend, la app no tiene mecanismo para forzar una nueva aceptación (no hay versión de T&C guardada, solo un booleano).
