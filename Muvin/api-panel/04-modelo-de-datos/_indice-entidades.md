# Índice de Entidades — Modelo de Datos

> **Última revisión:** 2026-04-21
> **Ver también:** [[diagrama-er-global]], [[glosario]]

---

## Entidades core (negocio)

| Entidad | Tabla | Modelo | Propósito |
|---------|-------|--------|-----------|
| Cupo | `cupo` | `common\models\Cupo` | Cupo de descarga, entidad central del sistema |
| Viaje | `viaje` | `common\models\Viaje` | Solicitud de transporte asignada |
| Carta de Porte | `carta_porte` | `common\models\CartaPorte` | Documento electrónico AFIP |
| Pedido | `pedido` | `common\models\Pedido` | Pre-solicitud de transporte |
| Cabecera | `cabecera` | `common\models\Cabecera` | Encabezado de contrato (v3) |
| Caratula MTR | `caratula_mtr` | `common\models\CaratulaMtr` | Caratula de mercado a término |

## Actores del sistema

| Entidad | Tabla | Modelo | Rol |
|---------|-------|--------|-----|
| User | `{{%user}}` | `common\models\User` | Usuario de acceso (IdentityInterface) |
| PersonaRol | `persona_rol` | `common\models\PersonaRol` | Actor polimórfico del sistema |
| Centro | (extends PersonaRol) | `common\models\Centro` | Terminal de descarga (rol=3) |
| Personas | `personas` | `common\models\Personas` | Datos personales |
| PersonaUsuario | `persona_usuario` | `common\models\PersonaUsuario` | Relación persona-usuario |

## Equipos y flota

| Entidad | Tabla | Modelo | Propósito |
|---------|-------|--------|-----------|
| ChoferEquipo | `chofer_equipo` | (app/models) | Vínculo chofer+camión+acoplado |
| Camion | `camion` | (app/models) | Vehículo tractor |
| Acoplado | `acoplado` | (app/models) | Semirremolque |

## Configuración de centros

| Entidad | Tabla | Modelo | Propósito |
|---------|-------|--------|-----------|
| ConfiguracionCentro | `configuracion_centro` | `common\models\ConfiguracionCentro` | Configuración operativa del centro |
| ConfiguracionDestino | `configuracion_destino` | `common\models\ConfiguracionDestino` | Config para destinos |
| ConfiguracionOrigen | `configuracion_origen` | `common\models\ConfiguracionOrigen` | Config para orígenes |
| ConfiguracionMuvin | `configuracion_muvin` | `common\models\ConfiguracionMuvin` | Parámetros globales del sistema |

## Relaciones entre actores y centros

| Entidad | Propósito |
|---------|-----------|
| `CentroCliente` | Centro ↔ Cliente |
| `CentroCorredor` | Centro ↔ Corredor |
| `CentroDestinatario` | Centro ↔ Destinatario |
| `CentroEntregador` | Centro ↔ Entregador |
| `CentroIntermediario` | Centro ↔ Intermediario |
| `CentroOperador` | Centro ↔ Operador |
| `CentroProducto` | Centro ↔ Producto (qué granos maneja) |
| `CentroTransporte` | Centro ↔ Transportista |

## Autenticación y seguridad

| Entidad | Tabla | Modelo | Propósito |
|---------|-------|--------|-----------|
| AccessTokens | `access_tokens` | `common\models\AccessTokens` | JWT tokens activos |
| AuthorizationCodes | `authorization_codes` | `common\models\AuthorizationCodes` | Códigos OAuth-like |
| ChangePassword | — | `common\models\ChangePassword` | Form model de cambio de clave |

## Notificaciones

| Entidad | Propósito |
|---------|-----------|
| `NotificacionCupera` | Notificaciones del sistema de cupos |
| `NotificacionesCentroCliente` | Notificaciones para centros |
| `NotificacionesRemitentes` | Notificaciones para remitentes |
| `NotificacionesTipo` | Catálogo de tipos |
| `NotificarEncuesta` | Encuestas a choferes post-viaje |

## Auditoría

| Entidad | Tabla | Propósito |
|---------|-------|-----------|
| `Auditoria` | `auditoria` | Registro de cambios (quién, qué, cuándo) |
| `AuditoriaVisto` | `auditoria_visto` | Registros ya leídos por usuarios |

## Catálogos

| Entidad | Propósito |
|---------|-----------|
| `Producto` | Granos y productos agropecuarios |
| `TipoProducto` | Tipos de producto |
| `ProductoCondicion` | Condiciones de calidad |
| `Provincias` | Provincias argentinas |
| `AfipLocalidades` | Localidades para CTG |
| `TipoDespacho` | Tipos de despacho |
| `TipoInterviniente` | Intervinientes en carta de porte |
| `CuitEmpresa` | Empresas registradas |
| `OnccaDestinoProducto` | Destinos ONCCA por producto |
| `OrigenDestino` | Pares origen-destino habilitados |
| `ZonaSolicitud` | Zonas geográficas |

## Flujos de negocio

| Entidad | Propósito |
|---------|-----------|
| `MotivoAnularCupo` | Por qué se anuló un cupo |
| `MotivoAnularSolicitud` | Por qué se anuló una solicitud |
| `MotivoRechazoDemandaCupo` | Por qué se rechazó una demanda |
| `MotivoRecuperarCupo` | Por qué se recuperó |
| `CaladaRechazada` | Registro de calada fallida en destino |
| `RechazarSolicitudes` | Solicitudes rechazadas |
| `RelacionCaratulasCupo` | Relación caratula AFIP ↔ cupo |
| `CartaPorteInterviniente` | Intervinientes en una CPe específica |
| `CartaPorteError` | Errores en emisión de CPe |
| `CartaPorteErrorDescripcion` | Descripción de error CPe |

## Novedades de terminales (sync)

| Entidad | Propósito |
|---------|-----------|
| `NovedadesTerminalesCupos` | Cupos nuevos publicados por terminales |
| `NovedadesTerminalesCuposProductos` | Productos de novedades |
| `NovedadesTerminalesFertilizantes` | Novedades de fertilizantes |

## Módulo ROSPORC / Oferta

| Entidad | Propósito |
|---------|-----------|
| `OfertaRosporc` | Oferta en Rosporc |
| `OfertaRosporcEntrega` | Detalle de entrega |
| `OfertaRosporcLote` | Lote de la oferta |
| `OfertaRosporcNovedades` | Novedades de Rosporc |
