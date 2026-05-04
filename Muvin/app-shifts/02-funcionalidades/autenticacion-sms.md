# Autenticación por SMS — app-shifts

## Descripción

La autenticación no usa contraseña tradicional. El chofer ingresa su número de teléfono y recibe un código SMS para verificar su identidad. El flujo tiene dos variantes: **chofer existente** y **chofer nuevo**.

## Flujo principal

```
SplashScreen
  → lee Preferences.ultimaPagina
    → si tiene token: HomePage
    → si no: LoginPage

LoginPage
  → ingresa teléfono
    → MuvinProvider.validarTelefono(telefono)
      → GET chofer-app/telefono?telefono=XXX
        → {existe: true, codigo: "1234"}   → PhoneVerificationPage (login)
        → {existe: false}                  → SignupPage (registro)

PhoneVerificationPage
  → ingresa código SMS
    → MuvinProvider.validarCodigoChoferExiste(telefono, codigo)
      → POST chofer-app/login {telefono, codigo}
        → {ok: true} → guarda accessToken → HomePage

SignupPage (chofer nuevo)
  → MuvinProvider.registrarTelefono(telefono)
    → POST site/register-phone {phone}
      → {ok: true, existe: true, datos: true}  → tiene datos → HomePage
      → {ok: true, existe: false}               → no registrado → CrearUsuarioPage
```

## Recuperación de cuenta

```
LoginPage → "¿Olvidaste tu cuenta?"
  → ingresa CUIT
    → MuvinProvider.solicitarRecuperacionCuenta(cuit)
      → POST chofer-app/solicitar-recuperarcion {cuit}
        → envía código SMS

  → ingresa código recibido
    → MuvinProvider.recuperarCuenta(codigo, cuit)
      → POST chofer-app/recuperar-cuenta {codigo, cuit, telefono}
        → guarda datos completos con _guardarConfigUsuario()
          → HomePage
```

## Endpoints involucrados

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `chofer-app/telefono?telefono={tel}` | Verifica si el teléfono existe |
| POST | `chofer-app/login` | Valida código SMS y obtiene token |
| POST | `site/register-phone` | Registra teléfono nuevo |
| POST | `chofer-app/solicitar-recuperarcion` | Solicita recuperación por CUIT |
| POST | `chofer-app/recuperar-cuenta` | Recupera cuenta con código + CUIT |

## Datos persistidos post-login

Después de autenticarse exitosamente, `_guardarConfigUsuario()` almacena en `Preferences`:
- `accessToken` (Bearer token)
- `nombre`, `apellidos`, `razonSocial`, `cuitCuil`, `telefono`, `email`, `domicilio`
- `idChofer`
- `patenteCamion`, `patenteAcoplado`, `idTipoCamion`, `idMarcaCamion`, `idTipoAcoplado`, `idMarcaAcoplado`
- `pais`, `provincia`, `localidad`

## Headers de autenticación

```dart
// Requests autenticadas
{
  'Authorization': 'Bearer ${Preferences().accessToken}',
  'X-Api-Key': Config.xApiKey   // 'uH9hatw8G3lQls7fE3'
}
```

> ⚠️ El `xApiKey` está hardcodeado en `config.dart`. Ver [[riesgos]].

---

Ver también: [[arranque]] · [[perfil-registro]] · [[verificar-cuit]]
