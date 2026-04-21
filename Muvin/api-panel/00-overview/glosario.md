# Glosario — Muvinapp

> **Última revisión:** 2026-04-21
> **Ver también:** [[vision-general]], [[modulo-cupos]], [[modulo-viajes]]

---

## Términos de negocio

| Término | Definición |
|---------|-----------|
| **Cupo** | Permiso de descarga emitido por una terminal para recibir un camión en fecha/hora determinada, para un producto y cosecha específicos |
| **Cupo terminal** | Identificador del cupo en el sistema de la terminal (idCupoTerminal) |
| **Carta de Porte (CPe)** | Documento electrónico de AFIP que acompaña legalmente el transporte de granos. Obligatorio desde Res. 5017/2021 |
| **CTG** (Código de Trazabilidad de Granos) | Código AFIP que habilita el tránsito de granos. Se genera al confirmar la CPe |
| **Caratula** | Encabezado de información del cupo: producto, cosecha, condiciones comerciales. Asociada al cupo en V3 |
| **Cabecera** | En módulo v3: agrupador de cupos por contrato/condición comercial |
| **Viaje** | Solicitud de transporte asignada a un chofer+equipo para un cupo específico |
| **Solicitud** | Estado previo al viaje: solicitud de transporte sin confirmar |
| **Pre-pedido / Pedido** | Estado inicial de demanda de transporte por parte del cargador |
| **Turno** | Horario asignado a un camión para ingresar a la terminal |
| **Turneada** | Sistema de gestión de turnos de ingreso a terminales |
| **Calada** | Proceso de pesaje y muestreo de la carga al ingresar al puerto/terminal |
| **Calada rechazada** | Calada fallida por no cumplir requisitos de calidad o peso |
| **Arribo intermedio** | Parada del camión en un punto intermedio entre origen y destino |
| **Desvío** | Cambio de destino de un cupo durante el trayecto |
| **Boca** | Punto de carga/descarga dentro de un centro |
| **Lista negra** | Registro de choferes o vehículos inhabilitados |
| **Premio/Sanción** | Registro de comportamiento histórico de un chofer |
| **Cosecha** | Año de cosecha del grano (ej: 2024) |
| **CUIT** | Clave Única de Identificación Tributaria (identificador fiscal argentino) |

---

## Actores del sistema

| Actor | `id_rol` | Descripción |
|-------|----------|-------------|
| **Administrador** | 1 | Acceso total al sistema |
| **Chofer** | 2 | Conductor del camión |
| **Centro** | 3 | Terminal/planta de descarga |
| **Transportista** | 4 | Empresa de transporte |
| **Cargador** | 5 | Dueño o remitente de la carga |
| **Destinatario** | 6 | Receptor de la carga |
| **Destino** | 7 | Punto físico de destino |
| **Entregador** | 8 | Operador de entrega |
| **Corredor** | 9 | Intermediario comercial |
| **Operador** | 11 | Empleado operativo del centro |
| **Consultor** | 12 | Solo lectura |
| **Marketing** | 13 | Acceso a módulo de marketing |
| **MAGYP** | 14 | Integración ministerio |
| **Fertilizantes** | 15 | Módulo fertilizantes |
| **MTR** | 16 | Módulo MATba/Rofex |

---

## Entidades técnicas principales

| Entidad (tabla) | Modelo PHP | Propósito |
|----------------|-----------|-----------|
| `cupo` | `Cupo` | Cupo de descarga |
| `viaje` | `Viaje` | Viaje asignado |
| `carta_porte` | `CartaPorte` | Carta de porte electrónica |
| `persona_rol` | `PersonaRol` | Actor del sistema (polimórfico) |
| `user` | `User` | Usuario de acceso |
| `access_tokens` | `AccessTokens` | Tokens JWT activos |
| `cabecera` | `Cabecera` | Encabezado de contrato v3 |
| `centro` | `Centro` → extends `PersonaRol` | Terminal de descarga |
| `configuracion_centro` | `ConfiguracionCentro` | Config por terminal |
| `chofer_equipo` | `ChoferEquipo` | Relación chofer-camión |
| `camion` | (en app/models) | Vehículo |
| `acoplado` | (en app/models) | Semirremolque |
| `pedido` | `Pedido` | Solicitud de transporte |
| `busqueda` | (en app/models) | Búsqueda de cupo |
| `auditoria` | `Auditoria` | Registro de cambios |
| `queue` | (yii2-queue) | Cola de trabajos async |

---

## Siglas y acrónimos

| Sigla | Significado |
|-------|-------------|
| **AFIP** | Administración Federal de Ingresos Públicos (Argentina) |
| **CTG** | Código de Trazabilidad de Granos |
| **CPe** | Carta de Porte Electrónica |
| **MAGYP** | Ministerio de Agricultura, Ganadería y Pesca |
| **MTR** | Mercado a Término (también módulo de integración con MATba/Rofex) |
| **RBAC** | Role-Based Access Control |
| **JWT** | JSON Web Token |
| **ERP** | Enterprise Resource Planning |
| **ORM** | Object Relational Mapper (Yii2 ActiveRecord) |
| **CUIT** | Clave Única de Identificación Tributaria |
| **RCT** | Registro de Caratulas de Tránsito |
| **STOP** | Sistema de Transporte de Granos (plataforma AFIP) |
| **CCPP** | Carta de Porte (Papel / Electrónica) |
| **REST** | Representational State Transfer |
| **SMS** | Short Message Service |
| **v1/v2/v3** | Versiones de la API de cupos |

---

## Estados del cupo

| Estado | Código | Descripción |
|--------|--------|-------------|
| Disponible | `D` | Cupo publicado, libre para tomar |
| Tomado | `T` | Asignado a un chofer |
| Activado | `A` | Chofer en camino |
| Arribado | `AR` | Chofer llegó a destino |
| Confirmado | `C` | Descarga confirmada |
| Anulado | `AN` | Cupo cancelado |
| Rechazado | `R` | Calada rechazada |
| Descargado | `DESC` | Descarga completada |

---

## Estados del viaje

| Estado | ID | Descripción |
|--------|-----|-------------|
| Pendiente | 1 | Solicitud creada |
| Tomado | 2 | Chofer aceptó |
| Activo | 3 | En trayecto |
| Desviado | 4 | Cambio de destino |
| Rechazado | 5 | Rechazado en destino |
| Llegado | 6 | Llegó a destino |
| Descargado | 7 | Descarga completada |
| Regresado | 8 | Retornó sin descargar |
| Cancelado | 9 | Anulado |
