# Clasificación Funcional de Archivos — app-clients

> [[README]] · [[tree-estructura-archivos]]

## Por categoría funcional

### Infraestructura / Core
| Archivo | Rol |
|---------|-----|
| `lib/main.dart` | Entry point, bootstrap |
| `lib/src/config/config.dart` | Configuración de entorno |
| `lib/src/config/preference.dart` | Persistencia local |
| `lib/src/providers/muvin_provider.dart` | Cliente HTTP |
| `lib/src/routes/routes.dart` | Tabla de rutas |
| `lib/src/blocs/provider.dart` | BlocProvider (DI) |
| `lib/src/blocs/validators.dart` | Validadores reactivos |

### Autenticación
| Archivo | Rol |
|---------|-----|
| `lib/src/pages/splash_page.dart` | Auto-login check |
| `lib/src/pages/login_page.dart` | Formulario login |
| `lib/src/pages/terminos_page.dart` | T&C |
| `lib/src/blocs/login_bloc.dart` | Lógica login |

### Navegación / Home
| Archivo | Rol |
|---------|-----|
| `lib/src/pages/home_page.dart` | Hub principal |
| `lib/src/menu/menu.dart` | Drawer condicional |

### Módulo Cupos
| Archivo | Rol |
|---------|-----|
| `lib/src/pages/cupos_page.dart` | Entry cupos Admin |
| `lib/src/pages/cuposcf_page.dart` | Entry cupos CF |
| `lib/src/pages/cupos/gestion_page.dart` | Filtros y listado |
| `lib/src/pages/cupos/solicitudes_page.dart` | Mis solicitudes |
| `lib/src/pages/cupos/solicitud_page.dart` | Alta solicitud |
| `lib/src/pages/cupos/solicitud_detail_page.dart` | Detalle |
| `lib/src/pages/cupos/busqueda_page.dart` | Búsqueda demandante |
| `lib/src/blocs/solicitud_cupo_bloc.dart` | BLoC cupos |
| `lib/src/blocs/busqueda_bloc.dart` | BLoC búsqueda |
| `lib/src/blocs/navegacion_cupos_bloc.dart` | BLoC navegación |
| `lib/src/blocs/cabecera_bloc.dart` | BLoC cabeceras |
| `lib/src/blocs/modificar_cant_cupo_bloc.dart` | BLoC modificar |

### Módulo Cargas
| Archivo | Rol |
|---------|-----|
| `lib/src/pages/gestion_carga_page.dart` | Entry cargas |
| `lib/src/pages/carga_cupos_disponibles_page.dart` | Cupos para cargar |
| `lib/src/pages/carga_nuevo_pedido_page.dart` | Nuevo pedido |
| `lib/src/blocs/pedido_bloc.dart` | BLoC pedidos |

### Modelos de datos
| Archivo | Rol |
|---------|-----|
| `lib/src/models/solicitud_model.dart` | Solicitud completa |
| `lib/src/models/solicitud_card_model.dart` | Tarjeta de solicitud |
| `lib/src/models/alfanumerico_model.dart` | Catálogo genérico |
| `lib/src/models/firebase_analytics_model.dart` | Evento analytics |
