# Árbol de Estructura de Archivos

> **Proyecto:** Muvinapp (app-panel)
> **Generado:** 2026-04-16
> **Total archivos en `src/`:** ~2.979
> **Archivos TypeScript:** ~1.711 (554 son `.spec.ts`)
> **Archivos HTML:** ~523
> **Archivos SCSS/CSS:** ~546

---

## Raíz del proyecto

```
app-panel/
├── angular.json                    # Configuración Angular CLI (proyecto "egret")
├── Dockerfile                      # Multi-stage: Node 12 build → Nginx Alpine serve
├── karma.conf.js                   # Config Karma (unit tests)
├── package.json                    # Dependencias (~90 deps, ~15 devDeps)
├── protractor.conf.js              # Config Protractor (E2E)
├── proxyconfig.json                # Proxy para desarrollo local
├── README.md                       # README por defecto (sin contenido útil)
├── tsconfig.json                   # Config TypeScript base
├── tslint.json                     # Linting (TSLint, deprecado)
├── typings.json                    # Typings legacy (sin uso moderno)
├── doc/
│   └── prompt.md                   # Prompt de documentación (no documentación real)
├── e2e/                            # Tests E2E (Protractor)
│   ├── app.e2e-spec.ts
│   ├── app.po.ts
│   └── tsconfig.e2e.json
└── src/                            # ← Código fuente principal
```

---

## `src/` — Código fuente

```
src/
├── index.html                      # HTML raíz (SPA entry point)
├── main.ts                         # Bootstrap Angular
├── polyfills.ts                    # Polyfills para navegadores
├── styles.css                      # Estilos globales base
├── test.ts                         # Entry point de tests unitarios
├── tsconfig.app.json               # TSConfig aplicación
├── tsconfig.spec.json              # TSConfig tests
├── typings.d.ts                    # Declaraciones de tipos globales
│
├── app/                            # ← Aplicación Angular
├── assets/                         # Recursos estáticos
├── environments/                   # Variables de entorno
└── vendor/                         # Librerías vendor embebidas
```

---

## `src/environments/` — Entornos

```
environments/
├── environment.ts                  # Dev: apunta a dev.muvinapp.com
└── environment.prod.ts             # Prod: misma URL dev ⚠️ URL_SERVICIOS apunta a localhost
```

> [!warning] Inversión de URLs en prod
> En `environment.prod.ts`, `URL_SERVICIOS` apunta a `http://localhost/api_muvin` mientras que en dev apunta a `https://dev.muvinapp.com/muvinapp-new-api/`. Esto parece invertido o requiere override en despliegue. Ver [[deuda-tecnica]].

---

## `src/assets/` — Recursos estáticos

```
assets/
├── i18n/                           # Internacionalización
│   ├── en.json                     # Inglés
│   └── es.json                     # Español
├── images/
│   ├── carta-porte/                # Imágenes carta de porte
│   ├── faces/                      # Avatares / fotos de perfil
│   ├── muvin/                      # Branding Muvin
│   └── products/                   # Imágenes de productos
└── styles/
    ├── app.scss                    # SCSS principal (entry point en angular.json)
    └── scss/                       # Parciales SCSS (variables, mixins, componentes)
```

---

## `src/vendor/` — Librerías embebidas

```
vendor/
├── Chart.min.js                    # Chart.js minificado (embebido, no de node_modules)
└── pace/                           # Pace.js — barra de carga de página
    ├── pace-green.css
    ├── pace-theme-min.css
    └── pace.min.js
```

> [!info] Duplicación
> Chart.js también está en `node_modules` vía `package.json`. El archivo en vendor podría ser una versión anterior. 💀 Posible código muerto.

---

## `src/app/` — Aplicación Angular (raíz)

```
app/
├── app.component.ts                # Componente raíz
├── app.component.html
├── app.component.css
├── app.component.spec.ts
├── app.module.ts                   # Módulo raíz (imports globales)
├── app.routing.ts                  # Routing principal (~18 rutas lazy-loaded)
├── fireservi.service.ts            # Servicio Firebase genérico
├── fireservi.service.spec.ts
│
├── shared/                         # ← Módulo compartido (servicios, modelos, componentes, etc.)
└── views/                          # ← Módulos funcionales por dominio (lazy-loaded)
```

---

## `src/app/shared/` — Módulo compartido

### Servicios (`shared/services/`) — ~80+ servicios

```
shared/services/
├── index.ts                        # Barrel export
├── auth/                           # Guards de autenticación (10 guards)
│   ├── auth.guard.ts               # Guard genérico (AuthGuard)
│   ├── term-auth.guard.ts          # Guard de términos y condiciones
│   ├── admin-auth.guard.ts         # Guard rol Admin
│   ├── centro-auth.guard.ts        # Guard rol Centro
│   ├── dador-auth.guard.ts         # Guard rol Dador
│   ├── destino-auth.guard.ts       # Guard rol Destino
│   ├── transp-auth.guard.ts        # Guard rol Transportista
│   ├── magyp-auth.guard.ts         # Guard rol MAGyP
│   ├── mtr-auth.guard.ts           # Guard rol MTR
│   ├── fertilizantes-auth.guard.ts # Guard rol Fertilizantes
│   └── index.ts
│
├── auth.service.ts                 # Servicio de autenticación principal
├── user.service.ts                 # Gestión de usuarios
├── layout.service.ts               # Estado del layout (sidebar, tema)
├── navigation.service.ts           # Menú de navegación dinámico
├── route-parts.service.ts          # Breadcrumbs
├── theme.service.ts                # Servicio de temas
├── cookies.service.ts              # Manejo de cookies
├── websocket.service.ts            # WebSocket (Socket.io)
│
│   # --- Dominio: Logística y transporte ---
├── camion.service.ts               # ABM camiones
├── acoplados.service.ts            # ABM acoplados
├── chofer.service.ts               # ABM choferes
├── equipos.service.ts              # ABM equipos (camión+acoplado)
├── transporte-chofer.service.ts    # Relación transporte-chofer
├── estados-chofer.service.ts       # Estados de choferes
├── zona-choferes-libres.service.ts # Choferes disponibles por zona
│
│   # --- Dominio: Centros y destinos ---
├── centros.service.ts              # ABM centros de acopio
├── destinos.service.ts             # ABM destinos
├── destinos-resolver.service.ts    # Resolver de destinos
├── tipo-destino.service.ts         # Tipos de destino
├── zona-destino.service.ts         # Zonas de destino
├── boca.service.ts                 # Bocas de descarga
│
│   # --- Dominio: Cupos y reservas ---
├── mapa-cupos.service.ts           # Mapas de cupos
├── lista-reserva.service.ts        # Listas de reserva
├── lista-turneada.service.ts       # Listas de turneada
├── reservas.service.ts             # Gestión de reservas
│
│   # --- Dominio: Personas y empresas ---
├── personas.service.ts             # ABM personas
├── destinatario.service.ts         # Destinatarios de carga
├── trabajadores.service.ts         # Trabajadores
│
│   # --- Dominio: Pedidos y viajes ---
├── pedido-resolver.service.ts      # Resolver de pedidos
├── consulta.service.ts             # Consultas
├── consultas.service.ts            # ⚠️ Duplicado de nombre con consulta.service
│
│   # --- Dominio: Productos y nomencladores ---
├── productos.service.ts            # ABM productos
├── centro-producto.service.ts      # Relación centro-producto
├── nomencladores.service.ts        # Nomencladores genéricos
├── origenes.service.ts             # ABM orígenes
│
│   # --- Dominio: Documentación ---
├── documento.service.ts            # Gestión de documentos
├── documentacion.service.ts        # Documentación de choferes/equipos
├── file-upload.service.ts          # Upload de archivos
│
│   # --- Dominio: Integración MAGyP ---
├── magyp.service.ts                # Servicio integración MAGyP
│
│   # --- Dominio: Fertilizantes ---
├── fertilizantes.service.ts        # Servicio fertilizantes
├── horario-fertilizantes.service.ts # Horarios de fertilizantes
├── horario-puerto.service.ts       # Horarios de puerto
│
│   # --- Dominio: Combustible ---
├── combustible.service.ts          # Retiros de combustible
│
│   # --- Dominio: Desvíos y rechazos ---
├── desvio-motivo.service.ts        # Motivos de desvío
├── lista-negra-motivos.service.ts  # Motivos lista negra
├── razon-rechazo.service.ts        # Razones de rechazo
│
│   # --- Dominio: Reportes y auditoría ---
├── reportes.service.ts             # Reportes
├── auditorias.service.ts           # Auditorías
├── auditoria-centro.service.ts     # Auditoría por centro
├── errorlog-centro.service.ts      # Log de errores por centro
│
│   # --- Dominio: Geolocalización ---
├── mapa-oficina.service.ts         # Mapa de oficinas
├── localidad.service.ts            # Localidades
├── provincia.service.ts            # Provincias
├── pais.service.ts                 # Países
├── zonas.service.ts                # Zonas
│
│   # --- Dominio: Marketing y comunicación ---
├── marketing.service.ts            # Marketing y noticias
├── promociones.service.ts          # Promociones
├── sendsms.service.ts              # Envío de SMS
├── message.service.ts              # Mensajería interna
├── operador-chat.service.ts        # Chat de operador
├── parametro-chat.service.ts       # Parámetros de chat
├── landing-page.service.ts         # Landing page
│
│   # --- Dominio: Otros ---
├── admin.service.ts                # Servicio administración
├── ccpp.service.ts                 # Carta de Porte
├── estandar.service.ts             # Estándares / documentos estándar
├── manual.service.ts               # Manuales
├── integracion-erp.service.ts      # Integración ERP
├── matbat.services.ts              # ⚠️ Nombre no estándar (.services)
├── ofertas.service.ts              # Ofertas
├── playas-intermedias.service.ts   # Playas intermedias
├── respuestas.service.ts           # Respuestas (a consultas)
├── situacion-puerto.service.ts     # Situación de puerto
├── terms.service.ts                # Términos y condiciones
│
│   # --- Utilidades ---
├── entity-mapper.service.ts        # Mapper genérico de entidades
├── mapper.service.ts               # Mapper de datos
├── exel.service.ts                 # ⚠️ Typo: "exel" → debería ser "excel"
│
│   # --- Datasources ---
├── pedido.datasource.ts            # Datasource para tabla de pedidos
├── provincia.datasource.ts         # Datasource para tabla de provincias
│
│   # --- Diálogos / UI Services ---
├── app-alert/                      # Componente diálogo alerta
├── app-atencion/                   # Componente diálogo atención
├── app-confirm/                    # Componente diálogo confirmación
├── app-error/                      # Componente diálogo error
├── app-loader/                     # Componente loader
│
└── utils/                          # Utilidades de servicio
```

### Modelos (`shared/models/`) — ~110+ modelos

```
shared/models/
├── index.ts                        # Barrel export
├── global.service.ts               # ⚠️ GlobalService vive en /models en vez de /services
│
│   # --- Entidades de dominio principal ---
├── pedido.ts                       # Pedido de transporte
├── pedidoModel.ts                  # ⚠️ Modelo pedido duplicado / alternativo
├── prepedido.ts                    # Pre-pedido
├── pedido-difusion.ts              # Pedido difundido
├── pedidoDifundido.ts              # ⚠️ Duplicado de nombre con pedido-difusion
├── viaje.ts                        # Viaje
├── viaje-lista.ts                  # Lista de viajes
├── hoja-ruta.ts                    # Hoja de ruta
├── seguimiento.ts                  # Seguimiento de viaje
├── siniestro.ts                    # Siniestro
│
│   # --- Entidades de actores ---
├── chofer.ts                       # Chofer
├── chofer-equipo.ts                # Equipo de chofer
├── chofer-premium.ts               # Chofer premium
├── chofer-zona.ts                  # Relación chofer-zona
├── chofer_por_evaluar.ts           # Choferes pendientes de evaluación
├── dador.ts                        # Dador de carga
├── corredor.ts                     # Corredor / operador
├── entregador.ts                   # Entregador
├── destinatario.ts                 # Destinatario
├── person.ts                       # Persona genérica
├── personRazonSocial.ts            # Persona con razón social
├── persona-rol.ts                  # Roles de persona
├── trabajador.ts                   # Trabajador
├── transporte-chofer.ts            # Relación transporte-chofer
├── user.ts                         # Usuario del sistema
├── userLog.ts                      # Log de usuario
├── postUser.ts                     # DTO creación de usuario
│
│   # --- Entidades de infraestructura logística ---
├── camion.ts                       # Camión
├── camiones-disponibles.ts         # Camiones disponibles
├── acoplado.ts                     # Acoplado / semirremolque
├── equipo.ts                       # Equipo (camión + acoplado)
├── marca-camion.ts                 # Marca de camión
├── marca-acoplado.ts               # Marca de acoplado
├── tipo-camion.ts                  # Tipo de camión
├── tipo-acoplado.ts                # Tipo de acoplado
│
│   # --- Entidades de centros y destinos ---
├── centro.ts                       # Centro de acopio / terminal
├── centro-producto.ts              # Relación centro-producto
├── destino.ts                      # Destino
├── destino-planta.ts               # Planta de destino
├── tipo-destino.ts                 # Tipo de destino
├── zona.ts                         # Zona geográfica
├── zona-destino.ts                 # Relación zona-destino
├── origen.ts                       # Origen de carga
├── boca.ts                         # Boca de descarga
├── puerto.ts                       # Puerto
├── playa-intermedia.ts             # Playa intermedia
│
│   # --- Entidades de cupos ---
├── cupo.ts                         # Cupo
├── cuposDisponibles.ts             # Cupos disponibles
├── cupos-disponibles-contrato.ts   # Cupos disponibles por contrato
├── cupoViculado.ts                 # ⚠️ Typo: "Viculado" → "Vinculado"
├── demanda-cupo.ts                 # Demanda de cupo
├── detalle-cupo.ts                 # Detalle de cupo
│
│   # --- Entidades de estados ---
├── estado.ts                       # Estado genérico
├── estadoDescarga.ts               # Estado de descarga
├── estadoViaje.ts                  # Estado de viaje
├── estado-puerto.ts                # Estado de puerto
├── viajeEstadoDescarga.ts          # Viaje + estado descarga
├── descarga.ts                     # Descarga
│
│   # --- Entidades de CCPP (Carta de Porte) ---
├── ccpp.ts                         # Carta de porte
├── cabecera.ts                     # Cabecera de carta de porte
│
│   # --- Entidades MAGyP ---
├── magyp-cadena.ts                 # Cadena MAGyP
├── magyp-contacto.ts               # Contacto MAGyP
│
│   # --- Entidades de geolocalización ---
├── localidad.ts                    # Localidad
├── provincia.ts                    # Provincia
├── pais.ts                         # País
├── marcador.ts                     # Marcador de mapa
├── mapa-oficina.ts                 # Oficina en mapa
│
│   # --- Entidades de documentación ---
├── documento.ts                    # Documento
├── documentos_choferes.ts          # Documentos de choferes
│
│   # --- Entidades de negocio auxiliar ---
├── producto.ts                     # Producto
├── product.model.ts                # ⚠️ Modelo de producto duplicado
├── consulta.ts                     # Consulta
├── respuesta.ts                    # Respuesta
├── desvio.ts                       # Desvío
├── desvioMotivo.ts                 # Motivo de desvío
├── inconsistencia.ts               # Inconsistencia en CCPP
├── motivo-rechazo-viaje.ts         # Motivo rechazo de viaje
├── motivoCaladaRechazada.ts        # Motivo de calada rechazada
├── ranking.ts                      # Ranking
├── retiro-combustible.ts           # Retiro de combustible
├── tipo-combustible.ts             # Tipo de combustible
├── inteligencia.ts                 # Inteligencia logística
├── insert-lote.ts                  # Insert de lote (reservas)
├── monitor-comercial.ts            # Monitor comercial
│
│   # --- Entidades de seguridad y permisos ---
├── roles.ts                        # Roles
├── roles-permissions.ts            # Permisos por rol
│
│   # --- Entidades de comunicación ---
├── notificacion.ts                 # Notificación
├── grupo_notificacion.ts           # Grupo de notificación
├── operador-chat.ts                # Chat de operador
├── parametro-chat.ts               # Parámetro de chat
│
│   # --- Entidades auxiliares ---
├── busqueda.ts                     # Búsqueda
├── filtros-reserva.ts              # Filtros de reserva
├── lista-reserva-models.ts         # Modelos de lista de reserva
├── lista-turneada-models.ts        # Modelos de lista turneada
├── listaNegraMotivo.ts             # Motivo de lista negra
├── situacion-puerto.ts             # Situación de puerto
├── terminales.model.ts             # Terminales
├── terms.ts                        # Términos y condiciones
├── promocion.model.ts              # Promoción
├── ErrorDataField.ts               # ⚠️ PascalCase incorrecto para archivo
├── event.model.ts                  # Evento
├── fertilizantes.model.ts          # Fertilizantes
├── horario-fertilizantes.ts        # Horario de fertilizantes
├── horario-puerto.ts               # Horario de puerto
│
│   # --- Paginación y respuestas ---
├── page.ts                         # Paginación
├── paged-data.ts                   # Datos paginados
├── response.ts                     # Respuesta genérica
├── response-body.ts                # Body de respuesta
├── respuestaHttp.ts                # Respuesta HTTP
│
│   # --- Versiones v2 (Cupera) ---
├── v2-demandados.ts                # Demandados v2
├── v2-disponibles.ts               # Disponibles v2
│
│   # --- Sistema de módulos ---
└── module-system.ts                # Sistema de módulos dinámico
```

### Componentes compartidos (`shared/components/`)

```
shared/components/
├── breadcrumb/                     # Breadcrumb de navegación
├── customizer/                     # Customizador de tema (Egret)
├── datatable-pager/                # Paginador para datatables
├── footer-socket/                  # Footer con estado de socket
├── header-menu/                    # Menú del header
├── header-side/                    # Header lateral
├── header-top/                     # Header superior
├── notifications/                  # Panel de notificaciones
├── sidebar-side/                   # Sidebar lateral
├── sidebar-top/                    # Sidebar superior
├── sidenav/                        # Sidenav (Angular Material)
├── layouts/                        # Layouts de la app
│   ├── admin-layout/               # Layout principal (autenticado)
│   └── auth-layout/                # Layout de sesión (login, registro)
│
│   # --- Componentes funcionales compartidos (lazy-loaded como módulo) ---
├── home/                           # 🔴 Módulo HOME — principal de operaciones (~70+ sub-componentes)
│   ├── home.module.ts
│   ├── home-routing.module.ts
│   ├── home.service.ts
│   ├── add-pedido/                 # Alta de pedido
│   ├── add-pedido-corto/           # Alta de pedido corto
│   ├── add-pedido-dador/           # Alta de pedido (vista dador)
│   ├── add-pedido-dador-corto/     # Alta pedido dador corto
│   ├── add-pedido-dador-retorno/   # Alta pedido dador retorno
│   ├── add-pedido-fertilizantes/   # Alta pedido fertilizantes
│   ├── add-pedido-masivo/          # Alta pedido masivo
│   ├── add-pedido-retorno/         # Alta pedido retorno
│   ├── asignar-viaje/              # Asignar viaje a chofer
│   ├── asignar-viaje-fertilizante/ # Asignar viaje fertilizante
│   ├── asignar-viaje-retorno/      # Asignar viaje retorno
│   ├── asignar-directo-v1/         # Asignación directa v1
│   ├── asignar-intermediarios/     # Asignar intermediarios
│   ├── carga/                      # Proceso de carga
│   ├── carga-cupos-excel/          # Carga de cupos desde Excel
│   ├── carga-masiva/               # Carga masiva
│   ├── carga-pedido/               # Carga de pedido
│   ├── confirm-carga/              # Confirmar carga
│   ├── confirm-carga-destino/      # Confirmar carga destino
│   ├── confirm-carga-parametros/   # Confirmar carga parámetros
│   ├── confirm-carga-retorno/      # Confirmar carga retorno
│   ├── confirmar-pedido/           # Confirmar pedido
│   ├── confirmar-pedido-retorno/   # Confirmar pedido retorno
│   ├── condiciones-viaje/          # Condiciones de viaje
│   ├── condiciones-viaje2/         # ⚠️ Versión duplicada de condiciones viaje
│   ├── configuracion-centro/       # Configuración del centro
│   ├── consulta-pedidos/           # Consulta de pedidos
│   ├── cupo-pedido/                # Cupo de pedido
│   ├── cupos-disponibles/          # Cupos disponibles
│   ├── cupos-vinculados/           # Cupos vinculados
│   ├── descarga/                   # Proceso de descarga
│   ├── desvio/                     # Registro de desvío
│   ├── desvio-retorno/             # Desvío retorno
│   ├── detalle-consulta/           # Detalle de consulta
│   ├── detalle-entrega/            # Detalle de entrega
│   ├── difusiones/                 # Difusiones de pedidos
│   ├── edit-pedido-fertilizantes/  # Editar pedido fertilizantes
│   ├── edit-viaje/                 # Editar viaje
│   ├── estado-descarga/            # Estado de descarga
│   ├── estado-viaje/               # Estado de viaje
│   ├── experiencia-acotada/        # Experiencia acotada (UI simplificada)
│   ├── extender-fecha/             # Extender fecha de pedido
│   ├── flota-intermediario/        # Flota de intermediario
│   ├── header-views/               # Headers de vistas
│   ├── info-subpedido/             # Info de subpedido
│   ├── info-viaje/                 # Info de viaje
│   ├── lista-choferes/             # Lista de choferes
│   ├── mapa/                       # Mapa de seguimiento
│   ├── mapa-cupos/                 # Mapa de cupos
│   ├── mesa-central/               # Mesa central de operaciones
│   ├── not-found/                  # 404 dentro de home
│   ├── panel-mesa-central/         # Panel mesa central
│   ├── patentes-chofer/            # Patentes del chofer
│   ├── pedido-dador/               # Pedido vista dador
│   ├── pedido-observaciones/       # Observaciones del pedido
│   ├── pedido-turneada/            # Pedido en turneada
│   ├── perfil/                     # Perfil de usuario
│   ├── por-evaluar/                # Pedidos por evaluar
│   ├── prepedido/                  # Pre-pedido
│   ├── productos-mezcla/           # Productos mezcla
│   ├── reducir/                    # Reducir cupos/pedidos
│   ├── seguimiento-oferta/         # Seguimiento de oferta
│   ├── seguimiento-reserva/        # Seguimiento de reserva
│   ├── seleccionar-pedido/         # Seleccionar pedido
│   ├── siniestro/                  # Registro de siniestro
│   ├── solicitudes/                # Solicitudes
│   ├── viaje/                      # Vista de viaje
│   ├── vincular-cliente/           # Vincular cliente
│   └── whatsapp/                   # Integración WhatsApp
│
├── cupo/                           # Módulo CUPO — gestión de cupos (~40+ sub-componentes)
│   ├── cupo.module.ts
│   ├── cupo-routing.module.ts
│   ├── cupo.service.ts
│   ├── services/                   # Servicios propios de cupo
│   ├── add-cupos-disponibles/
│   ├── add-cupos-solicitados/
│   ├── add-cupos-solicitados-v2/
│   ├── add-pedido-rapido/
│   ├── aplicar-cabecera/
│   ├── asignacion/
│   ├── asignacion-v2/
│   ├── asignados-receptor/
│   ├── asignar-sin-solicitud/
│   ├── asignar-solicitud/
│   ├── confeccion-ccpp/
│   ├── cupera3/                    # ⚠️ Versión 3 de cupera dentro de cupo
│   ├── cuponera/
│   ├── detalle-consolidado/
│   ├── detalle-dador/
│   ├── devolver/
│   ├── editar-cabecera/
│   ├── info-aplicar-cabecera/
│   ├── informacion-cupo/
│   ├── informacion-cupo-v2/
│   ├── informacion-demanda/
│   ├── listado-contrato/
│   ├── mapa-cupos/
│   ├── modals/
│   ├── modificar-carga-cupo/
│   ├── muvin-datepicker/           # Datepicker custom
│   ├── panel-consolidado/
│   ├── panel-consolidado-v2/
│   ├── rechazar-cupos/
│   ├── rechazar-solicitud/
│   ├── recuperar/
│   ├── recuperar-v2/
│   ├── seguimiento/
│   ├── solicitudes-cupo/
│   ├── turnos/
│   ├── usuario-sin-email/
│   └── ver-carta-porte/
│
├── combustible/                    # Módulo COMBUSTIBLE
│   ├── combustible.module.ts
│   ├── combustible-routing.module.ts
│   └── ordenes-retiro/             # Órdenes de retiro de combustible
│
├── turneada/                       # Módulo TURNEADA — gestión de turnos
│   ├── turneada.module.ts
│   ├── turneada-routing.module.ts
│   ├── confirmar-arribo/
│   ├── control/
│   ├── estado-choferes/
│   ├── lista-turneado/
│   ├── listar-lista/
│   └── turneada/
│
├── documentos/                     # Módulo DOCUMENTOS
│   ├── documentos.module.ts
│   ├── documentos-routing.module.ts
│   ├── choferes/
│   ├── mostrar-documento/
│   └── subir-documento/
│
└── derivar-reserva/                # Componente derivar reserva
```

### Directivas, Pipes, Helpers, etc.

```
shared/
├── directives/                     # Directivas custom (~15+)
│   ├── cdk-detail-row.directive.ts # Fila expandible en tabla
│   ├── cuit-validator.ts           # Validador CUIT
│   ├── cuit-validator2.ts          # ⚠️ Versión duplicada de validador CUIT
│   ├── dropdown-anchor.directive.ts
│   ├── dropdown-link.directive.ts
│   ├── dropdown.directive.ts
│   ├── egret-side-nav-toggle.directive.ts
│   ├── font-size.directive.ts
│   ├── is-chofer-cuit.directive.ts
│   ├── is-dador-cuit.directive.ts
│   ├── multipleEmails.ts
│   ├── scroll-to.directive.ts
│   ├── unique-email-persona.directive.ts
│   ├── unique-marca.directive.ts
│   ├── unique-patente-acoplado.directive.ts
│   ├── unique-patente-camion.directive.ts
│   └── unique-telefono-persona.directive.ts
│
├── pipes/                          # Pipes custom (8)
│   ├── auth-image.pipe.ts          # Pipe para imágenes autenticadas
│   ├── distancia.pipe.ts           # Formateo de distancia
│   ├── excerpt.pipe.ts             # Truncado de texto
│   ├── get-value-by-key.pipe.ts    # Obtener valor por clave
│   ├── integer-only.pipe.ts        # Solo enteros
│   ├── orderr-by.pipe.ts           # ⚠️ Typo: "orderr" → "order"
│   ├── relative-time.pipe.ts       # Tiempo relativo
│   └── smart-number.pipe.ts        # Formateo de números
│
├── helpers/                        # Helpers y utilidades
│   ├── CupoEstadosHelper.ts       # Helper estados de cupo
│   ├── CustomPaginatorConfiguration.ts
│   ├── date.adapter.ts            # Adaptador de fechas
│   ├── error.interceptor.ts       # Interceptor de errores HTTP
│   ├── jwt-interceptor.service.ts # Interceptor JWT
│   ├── url.helper.ts              # Helper de URLs
│   ├── utils.ts                   # Utilidades genéricas
│   ├── validation.ts              # Validaciones
│   └── window.helper.ts           # Helper de window
│
├── validation/                     # Validadores custom
│   └── customValidator.ts
│
├── maps/                           # Mappers de datos
│   ├── changeDataCamion.map.ts
│   ├── changeDataFilter.map.ts
│   └── InsertLoteReserva.map.ts
│
├── inmemory-db/                    # Base de datos en memoria (mock/demo)
│   ├── inmemory-db.service.ts
│   ├── calendarEvents.ts
│   ├── chat-db.ts
│   ├── countries.ts
│   ├── inbox.ts
│   ├── products.ts
│   ├── reservas-db.ts
│   ├── segimiento.ts              # ⚠️ Typo: "segimiento" → "seguimiento"
│   └── users.ts
│
├── animations/
│   └── egret-animations.ts        # Animaciones del template Egret
│
├── shared-material.module.ts       # Módulo de Angular Material compartido
└── shared.module.ts                # Módulo shared principal
```

---

## `src/app/views/` — Módulos funcionales (lazy-loaded)

```
views/
├── admin/                          # 🔴 ADMIN — Módulo de administración (~87 sub-secciones)
│   ├── admin.module.ts
│   ├── admin-routing.module.ts
│   │
│   │   # --- ABM de entidades principales ---
│   ├── acoplado/                   # CRUD acoplados
│   ├── camion/                     # CRUD camiones
│   ├── centros/                    # CRUD centros (con logo, tipo, variar-km)
│   ├── destinos/                   # CRUD destinos
│   ├── origenes/                   # CRUD orígenes
│   ├── bocas/                      # CRUD bocas de descarga
│   ├── equipos/                    # CRUD equipos
│   ├── productos/                  # CRUD productos
│   ├── productos-centro/           # Productos por centro
│   ├── personas/                   # CRUD personas (con perfil, roles, info)
│   ├── users/                      # CRUD usuarios
│   ├── roles/                      # CRUD roles
│   ├── roles-permissions/          # Permisos por rol
│   ├── corredor/                   # Gestión corredores
│   ├── destinatario/               # Gestión destinatarios
│   ├── entregador/                 # Gestión entregadores
│   ├── trabajadores/               # Gestión trabajadores
│   ├── documento/                  # CRUD documentos
│   ├── estandar/                   # CRUD estándares
│   ├── manual/                     # CRUD manuales
│   │
│   │   # --- Nomencladores geográficos ---
│   ├── pais/                       # CRUD países
│   ├── provincia/                  # CRUD provincias
│   ├── localidad/                  # CRUD localidades
│   │
│   │   # --- Configuración ---
│   ├── configurar-centro/          # Config de centro
│   ├── configurar-dadores/         # Config de dadores
│   ├── configurar-muvin/           # Config global de Muvin
│   │
│   │   # --- Vinculaciones (relaciones N:M) ---
│   ├── vincular-centro-cliente/
│   ├── vincular-centro-corredor/
│   ├── vincular-centro-destinatario/
│   ├── vincular-centro-empresa/
│   ├── vincular-centro-entregador/
│   ├── vincular-centro-intermediario/
│   ├── vincular-centro-operador/
│   ├── vincular-centro-transporte/
│   ├── vincular-transporte-chofer/
│   ├── vincular-zona-chofer/
│   │
│   │   # --- Gestión de choferes ---
│   ├── chofer-perdido/             # Choferes perdidos
│   ├── chofer-vencido/             # Choferes con docs vencidos
│   ├── choferes-uninstall/         # Choferes que desinstalaron app
│   ├── lista-choferes-estados/     # Estados de choferes
│   ├── edit-vencimiento-licencia/  # Editar vencimiento licencia
│   ├── busqueda-flota/             # Búsqueda de flota
│   │
│   │   # --- Listas y control ---
│   ├── lista-negra/                # Lista negra de choferes
│   ├── motivo-lista-negra/         # Motivos de lista negra
│   ├── lista-turneada/             # Listas de turneada
│   ├── lista-viajes-rechazados/    # Viajes rechazados
│   ├── gestionar-motivos/          # Motivos de gestión
│   ├── razon-rechazo/              # Razones de rechazo
│   ├── desvio-motivo/              # Motivos de desvío
│   │
│   │   # --- Mapas ---
│   ├── mapa-chofer-libre/          # Mapa choferes libres
│   ├── mapa-estaciones/            # Mapa estaciones de servicio
│   ├── mapa-oficina/               # Mapa oficinas
│   ├── mapa-radares/               # Mapa radares
│   ├── mapa-ruta/                  # Mapa rutas
│   ├── mapa-talleres/              # Mapa talleres
│   │
│   │   # --- Reportes y estadísticas ---
│   ├── reportes/                   # Reportes (report-centros)
│   ├── reporte-turneadas/          # Reporte de turneadas
│   ├── estadistica/                # Estadísticas (pedidos, viajes)
│   ├── rankings/                   # Rankings (choferes, desviados, rechazados)
│   │
│   │   # --- Auditoría y logs ---
│   ├── auditoria/                  # Auditoría global
│   ├── auditoria-centro/           # Auditoría por centro
│   ├── mostrar-logs/               # Visor de logs
│   │
│   │   # --- Puerto y destinos ---
│   ├── estado-puerto/              # Estado del puerto
│   ├── situacionpuerto/            # Situación de puerto
│   ├── tipo-destino/               # Tipos de destino
│   ├── zona-centro/                # Zonas de centro
│   ├── zona-choferes-libres/       # Zonas de choferes libres
│   ├── zona-destino/               # Zonas de destino
│   ├── playas-intermedias/         # Playas intermedias
│   │
│   │   # --- Combustible ---
│   ├── tipo-combustible/           # Tipos de combustible
│   │
│   │   # --- Logística ---
│   ├── logistica/                  # Vista logística
│   ├── confirmar-arribo/           # Confirmar arribo
│   ├── stop/                       # Stop de viaje
│   ├── hoja-ruta/                  # Hoja de ruta
│   ├── inteligencia/               # Inteligencia logística
│   │
│   │   # --- Operaciones masivas ---
│   ├── bajada-masiva/              # Bajada masiva
│   ├── empresas-masivas/           # Carga masiva de empresas
│   ├── asignar-empresa/            # Asignar empresa
│   ├── nuevos-proveedores/         # Nuevos proveedores
│   │
│   │   # --- Marketing y comunicación ---
│   ├── noticia/                    # CRUD noticias
│   ├── notificaciones/             # Notificaciones push
│   ├── concurso/                   # Concursos
│   ├── sorteo/                     # Sorteos
│   ├── promociones/                # Promociones
│   ├── parametro-chat/             # Parámetros de chat
│   ├── operador/                   # Gestión de operadores
│   │
│   │   # --- WhatsApp ---
│   ├── linea-whatsapp/             # Línea WhatsApp
│   ├── usuario-whatsapp/           # Usuarios WhatsApp
│   ├── whatsapp-clientes/          # WhatsApp clientes
│   ├── whatsapp-propios/           # WhatsApp propios
│   │
│   │   # --- Otros ---
│   ├── mis-centros/                # Mis centros
│   ├── consultas/                  # Consultas + responder
│   └── tipo-turneada/              # Tipos de turneada
│
├── ccpp/                           # CCPP — Carta de Porte Provisoria
│   ├── ccpp.module.ts
│   ├── ccpp-routing.module.ts
│   ├── cabecera/                   # Cabeceras CCPP (add, copia, ver, views-ccp)
│   ├── consulta/                   # Consulta CCPP (ver-ccpp)
│   ├── auditoria/                  # Auditoría CCPP
│   └── inconsistencia/             # Inconsistencias (notificar, ver)
│
├── cupera/                         # CUPERA — Gestión de cupos avanzada (v5)
│   ├── cupera.module.ts
│   ├── cupera-routing.module.ts
│   ├── components/                 # Componentes UI (asignación, contratos, cupos, seguimiento)
│   │   ├── asignacion-v5/
│   │   ├── contratos/
│   │   ├── cupera-v5-tabs/
│   │   ├── cupos-v5/
│   │   ├── motivo/
│   │   ├── seguimiento-v5/
│   │   └── view-ccpp-applicate/
│   ├── cupera-material/            # Módulo Material local
│   ├── models/                     # Modelos propios (~13 archivos)
│   ├── services/                   # Servicios propios
│   └── utils/                      # Utilidades
│
├── dador/                          # DADOR — Vista del dador de carga
│   ├── dador.module.ts
│   └── dador-routing.module.ts
│
├── dashboard/                      # DASHBOARD — Panel principal
│   ├── dashboard.module.ts
│   ├── dashboard-routing.module.ts
│   ├── dashboard.component.ts
│   ├── dashboard.component.html
│   └── dashboard.component.scss
│
├── destinatario/                   # DESTINATARIO — Vista del destinatario
│   ├── destinatario.module.ts
│   ├── destinatario-routing.module.ts
│   └── panel/
│
├── destino/                        # DESTINO — Gestión de planta destino / terminal
│   ├── destino.module.ts
│   ├── destino-routing.module.ts
│   ├── admin-bandas-horarias/      # Bandas horarias
│   ├── panel/                      # Panel principal destino
│   ├── plantas/                    # Plantas (clearing, pendientes, posición)
│   ├── tabla-horarios/             # Tabla de horarios
│   └── turnos/                     # Turnos (buscar, config-puerto, disponibles, resumen)
│
├── fertilizante/                   # FERTILIZANTE — Gestión de fertilizantes
│   ├── fertilizante.module.ts
│   ├── fertilizante.routing.ts
│   ├── admin-bandas/               # Bandas horarias de fertilizantes
│   ├── buscar/                     # Búsqueda
│   ├── comercial/                  # Comercial (reservas, seguimiento, detalles)
│   ├── components/                 # Componentes (editar fecha cupo)
│   ├── documentacion/              # Documentación
│   ├── gestion/                    # Gestión
│   ├── origenes/                   # Orígenes
│   ├── panel-reservas/             # Panel de reservas (capacidad, detalle, reservas)
│   ├── seguimiento/                # Seguimiento
│   └── tabla-horario-fertilizantes/ # Tabla horarios
│
├── magyp/                          # MAGyP — Integración con MAGyP
│   ├── magyp.module.ts
│   ├── magyp-routing.module.ts
│   ├── administracion/             # Administración (autoridades, cadenas, WhatsApp)
│   └── gestion/                    # Gestión (contacto, dashboard, detalle, seguimiento)
│
├── mtr/                            # MTR — Manifiesto de Transporte
│   ├── mtr.module.ts
│   ├── mtr-routing.module.ts
│   ├── caratulas/                  # Carátulas (dashboard-mtr, var-mercado)
│   ├── model/                      # Modelos propios
│   └── services/                   # Servicios propios
│
├── sessions/                       # SESSIONS — Autenticación
│   ├── sessions.module.ts
│   ├── sessions.routing.ts
│   ├── signin/                     # Login
│   ├── signup/                     # Registro
│   ├── forgot-password/            # Recuperar contraseña
│   ├── lockscreen/                 # Pantalla de bloqueo
│   ├── error/                      # Pantalla de error
│   ├── not-found/                  # 404
│   └── terms-and-conditions/       # Términos y condiciones
│
├── export/                         # EXPORT — Exportaciones de datos
│   ├── export.module.ts
│   ├── export-routing.module.ts
│   ├── export-excel.service.ts
│   ├── export-solicitudes-cupos/
│   ├── loaderExportCupos/
│   └── functions/
│
├── marketing/                      # MARKETING — Config y notificaciones manuales
│   ├── marketing.module.ts
│   ├── marketing-routing.module.ts
│   ├── configuracion/
│   └── notificaciones-manuales/
│
├── layout/                         # LAYOUT — ⚠️ Pendiente de verificar uso
│
└── reports/                        # REPORTS — Sistema de reportes
    ├── _index.ts
    ├── adapter.ts
    ├── service.ts
    ├── types.ts
    ├── configurations/
    ├── constants/
    └── functions/
```

---

## Resumen cuantitativo

| Métrica | Valor |
|---|---|
| Total archivos en `src/` | ~2.979 |
| Archivos TypeScript (`.ts`) | ~1.711 |
| Archivos de test (`.spec.ts`) | ~554 |
| Archivos HTML (templates) | ~523 |
| Archivos SCSS/CSS | ~546 |
| LOC TypeScript (sin specs) | ~9.746 ⚠️ |
| LOC HTML (templates) | ~27.668 |
| LOC SCSS/CSS | ~31.512 |
| Módulos lazy-loaded | ~18 |
| Servicios en `shared/services/` | ~80+ |
| Modelos en `shared/models/` | ~110+ |
| Guards de autenticación | 10 |
| Sub-componentes en Admin | ~87 |
| Sub-componentes en Home | ~70+ |
| Sub-componentes en Cupo | ~40+ |

> [!warning] LOC TypeScript bajo
> El conteo de ~9.746 LOC TS (sin specs) parece bajo para ~1.157 archivos .ts no-spec. Puede deberse a que muchos archivos son boilerplate corto (declaraciones de componentes, barrel exports, modelos de 10-20 líneas). La lógica pesada está mayormente en los templates HTML (~27k LOC).
