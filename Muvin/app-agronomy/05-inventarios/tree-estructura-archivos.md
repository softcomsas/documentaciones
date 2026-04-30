# Árbol de Estructura de Archivos — App Agronomy

> **Última revisión:** 2026-04-30

```
app-agronomy/
├── angular.json                    # Configuración Angular CLI + build personalizado
├── package.json                    # Dependencias npm (nombre: muvin-lite v14.2.0)
├── pnpm-lock.yaml                  # Lockfile de dependencias
├── tsconfig.json                   # Config TypeScript raíz
├── tsconfig.app.json               # Config TypeScript para build app
├── tsconfig.spec.json              # Config TypeScript para tests
├── webpack.config.js               # Config Webpack custom (dev)
├── webpack.prod.config.js          # Config Webpack custom (prod)
├── karma.conf.js                   # Config de tests unitarios
├── ngsw-config.json                # Config Service Worker (PWA)
├── Dockerfile                      # Imagen Docker de producción
├── nginx/
│   └── default.conf               # Config Nginx para servir la SPA
└── src/
    ├── index.html                  # HTML raíz de la app
    ├── main.ts                     # Bootstrap Angular
    ├── bootstrap.ts                # Bootstrap alternativo (⚠️ verificar uso)
    ├── config.ts                   # Configuración global extra (⚠️ verificar)
    ├── styles.css                  # Estilos globales CSS
    ├── styles.scss                 # Estilos globales SCSS
    ├── polyfills.ts                # Polyfills de compatibilidad
    ├── test.ts                     # Entrada de tests
    ├── manifest.webmanifest        # Manifest PWA
    ├── favicon.ico
    ├── typings.d.ts                # Declaraciones de tipos globales
    ├── environments/
    │   ├── environment.ts          # Variables de entorno (dev)
    │   └── environment.prod.ts    # Variables de entorno (prod)
    ├── assets/                     # Recursos estáticos
    └── app/
        ├── app.component.ts        # Componente raíz
        ├── app.component.html
        ├── app.component.css
        ├── app.module.ts           # Módulo raíz
        ├── app.routing.ts          # Rutas raíz (lazy loading)
        ├── helpers/                # Helpers globales de la app
        ├── pages/
        │   ├── sessions/           # Módulo de autenticación → [[modulo-sessions]]
        │   │   ├── sessions.module.ts
        │   │   ├── sessions-routing.module.ts
        │   │   ├── services/
        │   │   │   ├── auth.service.ts
        │   │   │   └── session.service.ts
        │   │   └── components/
        │   │       ├── signin/
        │   │       ├── forgot-password/
        │   │       ├── lockscreen/
        │   │       ├── not-found/
        │   │       ├── error/
        │   │       ├── profile/
        │   │       └── subir-logo/
        │   ├── pedidos/            # Módulo de pedidos → [[modulo-pedidos]]
        │   │   ├── pedidos.module.ts
        │   │   ├── pedidos-routing.module.ts
        │   │   ├── models/
        │   │   │   ├── camion.ts
        │   │   │   ├── chofer.ts
        │   │   │   ├── pedido-filter.ts
        │   │   │   ├── person.ts
        │   │   │   ├── producto.ts
        │   │   │   ├── proveedor.ts
        │   │   │   ├── response-pedido.ts
        │   │   │   └── response-reserva.ts
        │   │   ├── services/
        │   │   │   ├── pedidos.service.ts
        │   │   │   ├── reservas.service.ts
        │   │   │   ├── home.service.ts
        │   │   │   ├── origenes.service.ts
        │   │   │   └── fertilizantes.service.ts
        │   │   └── components/
        │   │       ├── pedidos/               # Listado principal
        │   │       ├── add-pedido/            # Alta de pedido
        │   │       ├── add-destination/
        │   │       ├── asignar-chofer/
        │   │       ├── asignar-directo/
        │   │       ├── camion/
        │   │       ├── detalle-cupos/
        │   │       ├── detalles-reserva-asignar/
        │   │       ├── detalles-reserva-cupos/
        │   │       ├── detalles-reserva-productos/
        │   │       ├── item-mezcla/
        │   │       ├── pedido-observaciones/
        │   │       ├── pick-chofer/
        │   │       ├── pick-patente/
        │   │       ├── productos-mezcla/
        │   │       ├── tipo-despacho/
        │   │       └── ver-cupos-reserva/
        │   └── transportistas/     # Módulo transportistas → [[modulo-transportistas]]
        │       ├── transportistas.module.ts
        │       ├── transportistas-routing.module.ts
        │       ├── models/
        │       ├── services/
        │       │   ├── acoplados.service.ts
        │       │   ├── camion.service.ts
        │       │   ├── centros.service.ts
        │       │   ├── chofer.service.ts
        │       │   ├── personas.service.ts
        │       │   └── user.service.ts
        │       └── components/
        │           ├── add-persona/
        │           ├── add-transportista/
        │           ├── camion-acoplado/
        │           ├── info-persona/
        │           └── transportistas/
        └── shared/                 # Módulo compartido → [[modulo-shared]]
            ├── shared.module.ts
            ├── shared-material.module.ts
            ├── index.ts
            ├── animations/
            ├── components/         # Layouts y componentes genéricos
            ├── directives/
            ├── guards/
            │   └── centro-auth.guard.ts
            ├── helpers/
            ├── inmemory-db/        # ⚠️ API in-memory (verificar si activo)
            ├── interceptors/
            ├── maps/               # Integración Google Maps
            ├── models/             # ~90 interfaces de dominio
            ├── pipes/
            ├── search/
            ├── services/           # ~24 servicios compartidos
            ├── utils/
            └── validators/
```
