# Flujo: Autenticación Completa

> [[_indice-flujos]] | Módulos: [[modulo-auth]] → [[modulo-home]]

## Descripción

Flujo completo desde el inicio de la app hasta el acceso al contenido principal. Cubre tres escenarios: primer ingreso, re-ingreso con token válido, y sesión expirada.

## Escenario 1: Primer ingreso

```mermaid
sequenceDiagram
    participant App
    participant SplashPage
    participant Preference
    participant LoginPage
    participant LoginBloc
    participant MuvinProvider
    participant TerminosPage
    participant HomePage

    App->>SplashPage: launch
    SplashPage->>Preference: getToken()
    Preference-->>SplashPage: null
    SplashPage->>LoginPage: pushReplacement('/login')
    
    LoginPage->>LoginBloc: changeEmail / changePassword
    LoginBloc-->>LoginPage: formValid = true
    LoginPage->>LoginBloc: login(email, pass)
    LoginBloc->>MuvinProvider: POST /login
    MuvinProvider-->>LoginBloc: {access_token, user...}
    LoginBloc->>Preference: guardar token + perfil
    LoginBloc->>TerminosPage: pushReplacement('/terminos')
    
    TerminosPage-->>Usuario: Mostrar T&C
    Usuario->>TerminosPage: "Acepto"
    TerminosPage->>Preference: setTerminosAceptados(true)
    TerminosPage->>HomePage: pushReplacement('/home')
```

## Escenario 2: Re-ingreso con token válido

```mermaid
sequenceDiagram
    participant SplashPage
    participant Preference
    participant HomePage

    SplashPage->>Preference: getToken()
    Preference-->>SplashPage: "eyJ..."
    SplashPage->>HomePage: pushReplacement('/home')
    Note over HomePage: Token no se valida contra backend
```

## Escenario 3: Sesión expirada (token inválido)

```mermaid
sequenceDiagram
    participant HomePage
    participant MuvinProvider
    participant Usuario

    HomePage->>MuvinProvider: GET cualquier_endpoint
    MuvinProvider-->>HomePage: 401 Unauthorized
    Note over HomePage: No hay interceptor de refresh
    Note over HomePage: La pantalla muestra error o se queda en blanco
    Note over Usuario: El usuario no sabe que debe re-loguearse
```

> 🔴 El escenario 3 es un problema de UX y seguridad. No hay redirección automática a `/login` cuando el token expira.

## Estados de datos al finalizar login exitoso

```
SharedPreferences:
  access_token    = "eyJ..."
  refresh_token   = "eyJ..."
  userId          = "42"
  userName        = "Juan Pérez"
  userEmail       = "juan@example.com"
  esDadorCupo     = true / false
  esClienteFinal  = true / false
  terminosAceptados = true
```
