# Módulos — Índice

La aplicación está organizada en tres capas principales: páginas (UI), BLoCs (lógica) y providers (servicios).

## Páginas (26 páginas)

| Ruta nombrada | Clase | Archivo |
|--------------|-------|---------|
| `/` (home por defecto) | `SplashScreen` | `splashscreen_page.dart` |
| `/home` | `HomePage` | `home_page.dart` |
| `/login` | `LoginPage` | `login_page.dart` |
| `/phoneverification` | `PhoneVerificationPage` | `phone_verification_page.dart` |
| `/logout` | `LoginPage` | *(redirige a login)* |
| `/signup` | `SignupPage` | `signup_page.dart` |
| `/intro` | `IntroPage` | `intro_page.dart` |
| `/cargas` | `CargasPage` | `cargas_page.dart` |
| `/cargasdetalle` | `CargasDetallePage` | `cargas_detalle_page.dart` |
| `/postulaciones` | `PostulacionesPage` | `postulaciones_page.dart` |
| `/viaje` | `ViajePage` | `viaje_page.dart` |
| `/cartaporte` | `CartaPortePage` | `carta_porte_page.dart` |
| `/crearusuario` | `CrearUsuarioPage` | `crear_usuario_page.dart` |
| `/usuario` | `UsuarioPage` | `usuario_page.dart` |
| `/turno` | `TurnoPage` | `turno_page.dart` |
| `/calada` | `CaladaPage` | `calada_page.dart` |
| `/actualizarusuario` | `ActualizarUsuarioPage` | `actualizar_usuario_page.dart` |
| `/consultas` | `ConsultasPage` | `consultas_page.dart` |
| `/compartir` | `CompartirPage` | `compartir_page.dart` |
| `/novedades` | `NovedadesPage` | `novedades_page.dart` |
| `/novedadesdetalle` | `NovedadesDetallePage` | `novedades_detalle_page.dart` |
| `/seguros` | `SegurosPage` | `seguros_page.dart` |
| `/beneficios` | `BenefioPage` | `beneficios_page.dart` *(typo en clase)* |
| `/filtro` | `FiltroPage` | `filtro_page.dart` |
| `/verificarcuit` | `VerificarCuitPage` | `verificar_cuit_page.dart` |
| `/verificarcodigoemail` | `VerificarCodigoEmailPage` | `verificar_codigo_email_page.dart` |

> ⚠️ Rutas comentadas en el código: `/politica` (PrivacidadPage), `/turno` tenía también `TurnoProxPage` comentado con nota "30-09 florencia, TKN#275".

## BLoCs (13)

| BLoC | Campos principales | Propósito |
|------|-------------------|-----------|
| `RegisterBloc` | nombre, apellido, email, teléfono... | Registro de nuevo chofer |
| `PhoneCodeBloc` | código SMS | Verificación de código telefónico |
| `UsuarioBloc` | cuit, nombre, apellidos, email, domicilio, localidad, marcaCamion, tipoCamion, patenteCamion, marcaAcoplado, tipoAcoplado, patenteAcoplado | Crear / actualizar perfil del chofer |
| `TurnoBloc` | puerto, producto, fecha, inicio, fin, textoCartaPorte, horario, textoCtg | Reserva y datos de turno de puerto |
| `CaladaBloc` | *(por inferencia: datos de calada)* | Proceso de calada |
| `CartaPorteBloc` | textoCartaPorte, textCtg | Carta de porte + CTG |
| `CompartirBloc` | telefono, nombre | Invitación por SMS |
| `ConsultaBloc` | mensaje, tipo | Consulta a operadores |
| `DenunciaSiniestroBloc` | *(datos del siniestro)* | Denuncia de accidente |
| `FiltroBloc` | centroCarga, localidadCarga, efectivoCarga, gasOILCarga | Filtros de búsqueda de cargas |
| `ValidarCuitBloc` | cuit | Validación de CUIT |
| `MotivoRechazoBloc` | *(motivo de rechazo)* | Motivo al rechazar calada |
| `ValidatorsBloc` (mixin) | — | StreamTransformers de validación compartidos |

## Providers (servicios, 6)

| Provider | Patrón | Descripción |
|---------|--------|-------------|
| `MuvinProvider` | Clase stateless | Todas las llamadas REST (~25 métodos) |
| `SocketProvider` | Clase con estado estático | Conexión Socket.IO persistente |
| `PushNotificationsProvider` | Singleton | Firebase Messaging — push notifications |
| `FirebaseAnalyticsProvider` | Singleton/static | Firebase Analytics observer |
| `GeolocatorProvider` | Clase | GPS — obtención de coordenadas |
| `ConnectivityProvider` | Clase | Verificación de red antes de cada request |

---

Ver también: [[endpoints-rest]] · [[chofer-model]] · [[flujo-turno]]
