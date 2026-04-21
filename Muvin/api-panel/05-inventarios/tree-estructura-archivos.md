# Árbol de Estructura de Archivos — Muvinapp API Panel

> **Generado:** 2026-04-21
> **Scope:** Proyecto completo excluyendo `vendor/`, `runtime/`, `.git/`
> **Total de archivos (sin vendor/runtime/git):** ~1.758

---

## Estructura raíz

```
api-panel/
├── .bowerrc                          # Configuración Bower (assets frontend legacy)
├── .gitignore
├── .gitlab-ci.yml                    # Pipeline CI/CD GitLab
├── .github/
│   └── workflows/
│       ├── deploy-dev.yml            # GitHub Actions: deploy automático a dev
│       └── sync-cap.yml              # Sincronización entornos
├── .htaccess                         # Reglas Apache (rewrite para Yii2)
├── codeception.yml                   # Config raíz de tests E2E (Codeception)
├── composer.json                     # Dependencias PHP (ver [[stack-tecnologico]])
├── daemons-app.json                  # ⚠️ Configuración de daemons (PM2 o similar)
├── docker-compose.yml                # Despliegue Docker (ver [[build-y-despliegue]])
├── Dockerfile                        # Imagen PHP/Apache
├── index.php                         # Entry point web (Yii2 bootstrap)
├── init                              # Script inicialización entornos (Unix)
├── init.bat                          # Script inicialización entornos (Windows)
├── install.sh                        # Script instalación dependencias
├── nodesource_setup.sh               # Setup Node.js en servidor
├── params.php                        # ⚠️ Parámetros globales (IDs hardcodeados, URLs, tokens)
├── README.md                         # README del repositorio
├── requirements.php                  # Verificador de requisitos PHP
├── Vagrantfile                       # Config Vagrant (entorno local legacy)
├── yii                               # CLI Yii2 (Unix)
├── yii.bat                           # CLI Yii2 (Windows)
│
├── backend/                          # App principal: API RESTful v1 + módulos
│   ├── actions/                      # Acciones reutilizables custom
│   │   └── CcppEditarAction.php      # Acción: edición de carta de porte electrónica
│   ├── assets/
│   │   ├── AppAsset.php              # Registro de assets web
│   │   ├── fondo-blanco.jpg/.png     # Assets de marca (PDF/email)
│   │   ├── logo-profertil.png        # Logo proveedor fertilizantes
│   │   ├── logoMuvin_rgb.png         # Logo Muvin
│   │   ├── logo_bcr.png              # Logo BCR (Bolsa de Comercio de Rosario)
│   │   └── seguro.png
│   ├── behaviours/                   # Filtros y behaviours de autenticación
│   │   ├── Apiauth.php               # 🔒 Auth por token (header x-access_token / query param)
│   │   ├── ControlDAcciones.php      # 🔒 Control de acciones por rol/usuario
│   │   ├── MyRule.php                # Regla custom de control de acceso
│   │   └── Verbcheck.php             # Verificación de verbo HTTP permitido
│   ├── components/
│   │   └── ControllerHelpersTrait.php # Trait con helpers para controladores
│   ├── config/
│   │   ├── bootstrap.php             # Bootstrap de la app backend
│   │   ├── cors.php                  # Configuración CORS (orígenes permitidos)
│   │   ├── main-local.php            # ⚠️ Config local (DB, credenciales — NO en repo)
│   │   ├── main.php                  # Configuración principal de la app
│   │   ├── rutas.php                 # Definición de todas las rutas (~392 líneas)
│   │   ├── swagger-local.php         # Config Swagger local
│   │   ├── swagger.php               # Config Swagger producción
│   │   └── test.php                  # Config entorno test (Codeception)
│   ├── controllers/                  # ~170 controladores REST (ver catálogo completo abajo)
│   ├── ElephantIO/                   # 🔌 Cliente WebSocket/Socket.IO (vendorizado localmente)
│   │   ├── Client.php
│   │   ├── Engine/
│   │   │   └── SocketIO/
│   │   │       ├── Version0X.php
│   │   │       ├── Version1X.php
│   │   │       └── Version2X.php     # ⚠️ Versión en uso
│   │   └── ...
│   ├── models/                       # Modelos ActiveRecord del dominio (~120 archivos)
│   ├── modules/                      # Módulos funcionales especializados
│   │   ├── agroquimicos/             # Módulo agroquímicos (PedidoController)
│   │   ├── bot/                      # Módulo chatbot
│   │   ├── erp/                      # Módulo ERP (DefaultController, FiltrarController)
│   │   ├── fertilizantes/            # Módulo fertilizantes (Cupo, Novedad, Proveedor, Reserva)
│   │   ├── magyp/                    # 🔌 Integración MAGYP/AFIP carta de porte electrónica
│   │   ├── mtr/                      # 🔌 Integración MTR (MATba/Rofex — mercado a término)
│   │   ├── service/                  # Módulo auth/RBAC proxy (microservicio externo)
│   │   ├── turneada/                 # Módulo turnos (Lista, Grupo, TurneadaGrupo)
│   │   ├── v2/                       # API v2 (CupoController — legacy)
│   │   └── v3/                       # API v3 (Cupo CCPP, Turnos, Demandas, Auditoría)
│   ├── runtime/                      # [ignorado] Logs, cache temporal de runtime
│   ├── tests/                        # Tests Codeception del backend
│   ├── views/                        # Vistas (usadas en PDFs/emails — minimal)
│   └── web/                          # Directorio web público (assets compilados)
│
├── common/                           # Código compartido entre backend/console
│   ├── components/                   # ~50 componentes/helpers reutilizables
│   │   ├── AccessRuleMuvin.php       # 🔒 Regla de acceso custom
│   │   ├── ApiResponse.php           # Envoltura estándar de respuestas API
│   │   ├── AsyncCurl.php             # Cliente HTTP async (logging gateway)
│   │   ├── BaseCurl.php              # Cliente HTTP base
│   │   ├── BusIntegracion.php        # 🔌 Bus de integración (VTerra)
│   │   ├── CacheHelper.php           # Helper de cache (FileCache)
│   │   ├── ChatBotAuth.php           # 🔒 Autenticación del chatbot
│   │   ├── CheckAccess.php           # 🔒 Verificación de permisos RBAC
│   │   ├── CheckRolesTrait.php       # Trait de verificación de roles
│   │   ├── CosechaHelper.php         # Helper para cálculo de cosecha
│   │   ├── CuitValidator.php         # Validador CUIT argentino
│   │   ├── EnviarNotificacion.php    # Envío de notificaciones push/socket
│   │   ├── ExceptionHandler.php      # Manejo centralizado de excepciones
│   │   ├── FilterMethods.php         # Behaviour: filtrado y sanitización de inputs
│   │   ├── GenerarExcel.php          # 📊 Generador de Excel (PHPSpreadsheet)
│   │   ├── IntegracionAfip.php       # 🔌 Integración AFIP (carta de porte)
│   │   ├── IntegracionAfipMonsanto.php # 🔌 Integración AFIP variante Monsanto/Bayer
│   │   ├── JwtValidationData.php     # 🔒 Validación de tokens JWT
│   │   ├── MicroServicio.php         # Cliente base para microservicios
│   │   ├── MicroServicioRbac.php     # 🔒 Cliente microservicio RBAC
│   │   ├── MtrConnect.php            # 🔌 Conexión MTR/MATba
│   │   ├── NotificarCp.php           # Notificación carta de porte
│   │   ├── PdfResources.php          # 📄 Recursos para generación de PDFs (mPDF)
│   │   ├── RecuperacionPermisos.php  # Recuperación/cache de permisos de usuario
│   │   ├── RefreshToken.php          # 🔒 Lógica de refresh de JWT
│   │   ├── Socket.php                # 🔌 Wrapper ElephantIO para WebSockets
│   │   ├── StopConnect.php           # 🔌 Conexión STOP (datos CTG de AFIP)
│   │   ├── StrongTokenAuth.php       # 🔒 Auth con token fuerte (variante)
│   │   ├── TokenHelper.php           # 🔒 Helper JWT (encode/decode)
│   │   ├── Turnos.php                # Lógica de gestión de turnos
│   │   ├── UserComponent.php         # 🔒 Componente User custom de Yii2
│   │   └── ...
│   ├── config/
│   │   ├── main-local.php            # ⚠️ Config local DB y credenciales (NO en repo)
│   │   └── main.php                  # Config común (queue, cache, logService, mtrConnect)
│   ├── dto/                          # Data Transfer Objects
│   ├── exceptions/                   # Excepciones custom
│   ├── fixtures/                     # Fixtures de tests (Faker)
│   ├── jobs/                         # 🔄 Jobs para yii2-queue (async)
│   ├── mail/                         # Plantillas de email (SwiftMailer)
│   ├── models/                       # Modelos comunes (User, AccessTokens, CartaPorte, etc.)
│   │   ├── AccessTokens.php          # 🔒 Modelo de tokens de acceso OAuth2
│   │   ├── CartaPorte.php            # Modelo carta de porte electrónica (AFIP)
│   │   ├── CartaPorteError.php       # Errores de carta de porte
│   │   ├── CartaPorteInterviniente.php
│   │   ├── Cupo.php                  # Modelo central: cupo de descarga
│   │   ├── CupoAsignado.php
│   │   ├── CupoCliente.php
│   │   ├── CupoHistorial.php
│   │   ├── DemandaCupo.php
│   │   ├── LoginForm.php
│   │   ├── LoginFormIntegracion.php
│   │   ├── User.php                  # 🔒 Modelo de usuario (JWT identity)
│   │   └── ...
│   ├── tests/
│   └── validators/                   # Validadores custom
│
├── console/                          # App de consola: cron jobs y migraciones
│   ├── config/
│   ├── controllers/                  # Controladores CLI
│   │   ├── AfipController.php        # 🔄 Consultas AFIP (CTG/Stop)
│   │   ├── ChoferController.php      # 🔄 Procesamiento batch de choferes
│   │   ├── ClearCacheController.php  # 🔄 Limpieza de cache
│   │   ├── CronJobCuitController.php # 🔄 Cron CUIT empresa
│   │   ├── CupoController.php        # 🔄 Procesamiento de cupos
│   │   ├── DemandasController.php    # 🔄 Procesamiento de demandas
│   │   ├── DestinoController.php     # 🔄 Procesamiento de destinos
│   │   ├── ImportIntegracionshController.php # 🔄 Importación de integraciones
│   │   ├── MigracionController.php   # 🔄 Migraciones custom
│   │   ├── NotificarCpController.php # 🔄 Notificación carta de porte
│   │   ├── TimbuesController.php     # 🔄 Integración Timbues
│   │   └── TestController.php
│   ├── migrations/                   # 623 migraciones de base de datos
│   ├── models/
│   ├── runtime/                      # [ignorado]
│   └── log.cronAfip.txt              # ⚠️ Log de cron AFIP commiteado en repo
│
├── img/                              # Imágenes estáticas del proyecto
├── runtime/                          # [ignorado] Runtime raíz
├── script/
│   ├── cuit_empresa.sql              # Script SQL: carga de CUIT empresas
│   ├── script limpia centro.sql      # Script SQL: limpieza de centros
│   └── validaciones.sql              # Script SQL: validaciones manuales
├── vagrant/                          # Configuración Vagrant (desarrollo local legacy)
└── vendor/                           # [ignorado] Dependencias Composer
```

---

## Catálogo de controladores V1 por dominio

> Todos en `backend/controllers/`. Heredan de `ApiRestController extends ActiveController`.

### 🔒 Autenticación y usuarios
| Archivo | Propósito |
|---------|-----------|
| `LoginController.php` | Login, login-panel, access-token (JWT) |
| `ApiUsuarioController.php` | Registro de usuarios externos |
| `UsuarioController.php` | Gestión de usuarios del panel |
| `PersonaUsuarioController.php` | Relación persona-usuario |
| `PersonaRolController.php` | Consulta de personas por rol/CUIT |
| `RolesController.php` | CRUD de roles |
| `RolControlesController.php` | Permisos por rol |
| `PermisosPorUsuarioController.php` | Permisos individuales |
| `ModulosUsuarioController.php` | Módulos asignados al usuario |
| `MenuDinamicoController.php` | Menú dinámico por rol |
| `TestJwtFirebaseController.php` | 💀 Test de integración JWT (dev only) |

### 📦 Gestión de cupos
| Archivo | Propósito |
|---------|-----------|
| `CupoController.php` | CRUD principal de cupos |
| `ApiCupoController.php` | API pública de cupos (v1) |
| `CupoClienteController.php` | Cupos por cliente |
| `CupoFertilizanteController.php` | Cupos fertilizantes |
| `GestionadorCupoController.php` | Panel de gestión de cupos |
| `DemandaCupoController.php` | Demandas de cupo |
| `DemandaCaratulaController.php` | Demandas con carátula MTR |
| `DemandaEstadoController.php` | Estado de demandas |
| `ApiCupoController.php` | Asignación masiva, CTG update |

### 🚚 Choferes y vehículos
| Archivo | Propósito |
|---------|-----------|
| `ChoferController.php` | CRUD choferes (vinculados a transportistas) |
| `ChoferAppController.php` | API para app móvil del chofer |
| `ChoferHuerfanoController.php` | Choferes sin vinculación |
| `ChoferPerdidoController.php` | Choferes fuera de zona |
| `ChoferPostuladoController.php` | Choferes postulados |
| `ChoferEquipoController.php` | Equipos de choferes |
| `DocumentacionChoferController.php` | Documentación (carnet, etc.) |
| `BusquedaChoferController.php` | Búsqueda de choferes |
| `BusquedaChoferHuerfanoController.php` | Búsqueda huérfanos |
| `HistoricoChoferLibreController.php` | Histórico de choferes libres |
| `HistoricoPremioSancionController.php` | Premios y sanciones |
| `EstadoChoferController.php` | Estados del chofer |
| `ListaChoferesController.php` | Listas de choferes |
| `CamionController.php` | CRUD camiones |
| `AcopladoController.php` | CRUD acoplados |
| `EquipoController.php` | Equipos (camión+acoplado) |
| `MarcaCamionController.php` | Marcas de camión |
| `MarcaAcopladoController.php` | Marcas de acoplado |
| `TipoCamionController.php` | Tipos de camión |
| `TipoAcopladoController.php` | Tipos de acoplado |
| `BusquedaTipoAcopladoController.php` | Búsqueda por tipo |

### 🏭 Centros y terminales
| Archivo | Propósito |
|---------|-----------|
| `CentroController.php` | CRUD centros (plantas/terminales/bocas) |
| `CentroClienteController.php` | Clientes por centro |
| `CentroCorredorController.php` | Corredores por centro |
| `CentroDestinatarioController.php` | Destinatarios por centro |
| `CentroEntregadorController.php` | Entregadores por centro |
| `CentroIntermediarioController.php` | Intermediarios por centro |
| `CentroOperadorController.php` | Operadores por centro |
| `CentroProductoController.php` | Productos por centro |
| `CentroTransporteController.php` | Transportistas por centro |
| `ConfiguracionCentroController.php` | Configuración de centro |
| `ListaCentroController.php` | Listas de centros |
| `EvaluacionTerminalController.php` | Evaluaciones de terminales |
| `HorarioPuertoController.php` | Horarios de puerto |
| `SituacionPuertoController.php` | Estado en tiempo real del puerto |
| `TurnoPuertoController.php` | Turnos en puerto |

### 👥 Actores del negocio
| Archivo | Propósito |
|---------|-----------|
| `DadorController.php` | Dadores de carga |
| `DadorReceptorController.php` | Dadores-receptores |
| `CorredorController.php` | Corredores de granos |
| `TransportistaController.php` | Empresas transportistas |
| `TransportistaPostuladoController.php` | Transportistas postulados |
| `OperadorController.php` | Operadores logísticos |
| `EntregadorController.php` | Entregadores |
| `IntermediarioController.php` | Intermediarios |
| `IntermediarioPostuladoController.php` | Intermediarios postulados |
| `DestinatarioController.php` | Destinatarios de carga |
| `ConsultorController.php` | Consultores |

### 🗺️ Destinos, zonas y geografía
| Archivo | Propósito |
|---------|-----------|
| `DestinoController.php` | CRUD destinos |
| `BocaController.php` | Bocas de descarga |
| `DestinoPersonaController.php` | Personas por destino |
| `DestinoProductoController.php` | Productos por destino |
| `DestinoEntregadorCentroController.php` | Rel. destino-entregador-centro |
| `TipoDestinoController.php` | Tipos de destino |
| `ZonaDestinoController.php` | Zonas de destino |
| `ZonasController.php` | Zonas generales |
| `ZonaHuerfanoController.php` | Zonas huérfanas |
| `OrigenController.php` | Orígenes de carga |
| `HorarioOrigenController.php` | Horarios de origen |
| `LocalidadController.php` | Localidades |
| `ProvinciaController.php` | Provincias |
| `PaisController.php` | Países |
| `PlayaIntermediaController.php` | Playas intermedias |

### 🛤️ Viajes y solicitudes
| Archivo | Propósito |
|---------|-----------|
| `ViajeController.php` | CRUD viajes |
| `ViajeEstadoController.php` | Estados de viaje |
| `SolicitudController.php` | Solicitudes de transporte |
| `AnularSolicitudController.php` | Anulación de solicitudes |
| `PedidoController.php` | Pedidos de transporte |
| `PrePedidoController.php` | Pre-pedidos |
| `PedidoCondicionesController.php` | Condiciones de pedido |
| `PedidoTipoAcopladoController.php` | Tipos de acoplado por pedido |
| `PedidoZonaIdealController.php` | Zona ideal de pedido |
| `DesvioController.php` | Desvíos de viaje |
| `DesvioMotivoController.php` | Motivos de desvío |
| `MotivoRechazoViajeController.php` | Motivos de rechazo |

### ⚓ Arribo, calada y auditoría
| Archivo | Propósito |
|---------|-----------|
| `ArribosIntermediosController.php` | Arribes intermedios |
| `CaladaRechazadaController.php` | Caladas rechazadas |
| `MotivoCaladaRechazadaController.php` | Motivos de rechazo de calada |
| `AuditoriaInternaController.php` | Auditoría interna de operaciones |
| `ActualizarPagoTasaController.php` | Actualización de pago de tasa |
| `PdfArribosController.php` | 📄 Generación PDF de arribes |

### 💬 Comunicación y notificaciones
| Archivo | Propósito |
|---------|-----------|
| `ChatBotController.php` | Bot de WhatsApp/chat |
| `ParametrosChatController.php` | Parámetros del chatbot |
| `OperadoresChatController.php` | Operadores del chat |
| `MessengerController.php` | Integración SMS (Infobip) |
| `WhatsAppController.php` | Integración WhatsApp |
| `GrupoNotificacionesController.php` | Grupos de notificaciones push |
| `NotificacionesCentroClienteController.php` | Notificaciones por cliente-centro |
| `NotificacionesTipoAcopladoController.php` | Notificaciones por tipo acoplado |
| `NoticiasController.php` | Noticias/comunicados del sistema |

### ⚙️ Configuración y catálogos
| Archivo | Propósito |
|---------|-----------|
| `ConfiguracionMuvinController.php` | Config global del sistema |
| `ConfiguracionDestinoController.php` | Config de destinos |
| `ProductoController.php` | Productos (granos, fertilizantes) |
| `TipoPersonaController.php` | Tipos de persona |
| `TipoCombustibleController.php` | Tipos de combustible |
| `TipoSiniestroController.php` | Tipos de siniestro |
| `TipoTurneadaController.php` | Tipos de turneada |
| `MedioPagoController.php` | Medios de pago |
| `EstadoController.php` | Estados genéricos del sistema |
| `EstandarController.php` | Estándares de calidad |
| `TerminoController.php` | Términos y condiciones |
| `MuvinController.php` | Información general Muvin |
| `CuitEmpresaController.php` | CUITs de empresa |

### 📊 Mapas, tracking y reportes
| Archivo | Propósito |
|---------|-----------|
| `SeguimientoController.php` | Seguimiento en tiempo real |
| `MapaOficinaLnhController.php` | Mapa de oficinas (LNH) |
| `MapaOficinaRutaController.php` | Mapa de rutas de oficinas |
| `MapaRadarController.php` | Radar de posición |
| `MapaTallerController.php` | Mapa de talleres |
| `SelectController.php` | 📊 Datos para selects/dropdowns |
| `BusquedaController.php` | Búsqueda general |

### 🎲 Otros
| Archivo | Propósito |
|---------|-----------|
| `SiniestroController.php` | Siniestros de transporte |
| `RetiroCombustibleController.php` | Retiros de combustible |
| `ConcursoController.php` | Concursos/sorteos |
| `GanadoresSorteoController.php` | Ganadores de sorteos |
| `PromocionController.php` | Promociones |
| `ListaNegraController.php` | Lista negra de actores |
| `ListaNegraMotivoController.php` | Motivos de lista negra |
| `OfertaRosporcController.php` | 🔌 Oferta Rosporc (mercado porcino) |
| `RecursosExternosController.php` | Recursos externos |
| `DocumentacionesController.php` | Documentaciones |
| `DocumentoController.php` | Documentos |
| `ManualController.php` | Manual de usuario |
| `SwaggerController.php` | Documentación OpenAPI/Swagger |
| `SiteController.php` | Endpoints generales (imágenes, etc.) |

---

## Notas y hallazgos

- ⚠️ `params.php` en la raíz del repo contiene IDs hardcodeados, tokens de servicio (`s-roles-permisos.token: '123456'`), y URLs de entornos. **Riesgo de seguridad.**
- ⚠️ `console/log.cronAfip.txt` commiteado en el repositorio — expone logs de producción.
- ⚠️ `ElephantIO` está vendorizado localmente dentro de `backend/` en lugar de gestionarse vía Composer. Esto rompe la trazabilidad de versiones.
- 💀 `TestJwtFirebaseController.php` parece un controlador de prueba; verificar si está expuesto en producción.
- 🚧 `daemons-app.json` presente en raíz — posiblemente configuración PM2; no documentado.
- 🔴 El directorio `script/` contiene SQL sueltos que no son migraciones Yii2 formales. No hay garantía de reproducibilidad.
