# Árbol de Estructura de Archivos — full-platform-main

> **Generado:** 2026-04-29
> **Fuente:** Análisis directo del repositorio

```
full-platform-main/
├── README.md                          # Documentación general del monorepo
├── package.json                       # Dependencias raíz del workspace (@aisa-plataforma/source)
├── pnpm-lock.yaml                     # Lockfile de pnpm
├── nx.json                            # Configuración de Nx (tareas, caché, plugins)
├── tsconfig.base.json                 # TypeScript config base compartida
├── tailwind.config.js                 # Tailwind CSS config raíz
├── jest.config.ts                     # Jest config raíz
├── jest.preset.js                     # Preset Jest para Nx
├── .eslintrc.json                     # ESLint config raíz
├── .prettierrc                        # Prettier config
├── .gitlab-ci.yml                     # Pipeline CI/CD GitLab
├── .github/                           # GitHub Actions (algunos submódulos)
│
├── main/                              # 🏠 Shell Angular 16 (Module Federation Host)
│   ├── src/
│   │   ├── app/
│   │   │   ├── app.component.ts
│   │   │   ├── app.module.ts
│   │   │   ├── app.routes.ts          # Rutas con lazy MFEs
│   │   │   ├── contacts/              # Módulo de contactos embebido
│   │   │   ├── custom-layout/         # Vex layout (sidebar, header)
│   │   │   ├── guards/                # Auth guards
│   │   │   └── helpers/
│   │   ├── @vex/                      # ⚠️ Tema Vex embebido como código fuente
│   │   ├── assets/
│   │   └── static-data/
│   ├── module-federation.config.js    # Config MFE host
│   ├── webpack.config.js
│   ├── webpack.prod.config.js
│   └── proxy.conf.json                # Proxy dev → backends locales
│
├── auth-app/                          # 🔐 Autenticación
│   ├── backend/
│   │   └── api/
│   │       └── README.md              # ⚠️ Backend stub — sin implementación
│   └── frontend/
│       └── src/
│           ├── app/
│           │   ├── auth/              # Módulo de login
│           │   └── remote-entry/
│           ├── @vex/                  # Vex theme (copia)
│           ├── mf-login/              # Componente login para modo MFE
│           └── styles/
│
├── descargas-app/                     # 🌾 Descargas (módulo más complejo)
│   ├── backend/                       # NestJS 10
│   │   ├── src/
│   │   │   ├── app.module.ts
│   │   │   ├── main.ts
│   │   │   ├── controllers/           # 8 controladores REST
│   │   │   │   ├── descargas.controller.ts
│   │   │   │   ├── reportes.controller.ts
│   │   │   │   ├── transferencia.controller.ts
│   │   │   │   ├── adenda.controller.ts
│   │   │   │   ├── tren.controller.ts
│   │   │   │   ├── cron.controller.ts
│   │   │   │   ├── txt-upload.controller.ts
│   │   │   │   └── consultar-roles.controller.ts
│   │   │   ├── services/              # 13 servicios
│   │   │   │   ├── descargas.service.ts
│   │   │   │   ├── descargas_extra.service.ts
│   │   │   │   ├── reportes.service.ts
│   │   │   │   ├── external.api.service.ts  # 🔌 AFIP + APIs externas
│   │   │   │   ├── cron.service.ts
│   │   │   │   ├── cp-tren.service.ts
│   │   │   │   ├── adenda.service.ts
│   │   │   │   ├── transferencia.service.ts
│   │   │   │   ├── upload-txt.service.ts
│   │   │   │   ├── valoresDeCalidad.service.ts
│   │   │   │   ├── logs.service.ts
│   │   │   │   └── mapCartaPorteResponse.ts
│   │   │   ├── modules/               # 7 módulos NestJS
│   │   │   │   ├── descargas.module.ts
│   │   │   │   ├── reportes.module.ts
│   │   │   │   ├── cron.module.ts
│   │   │   │   ├── txt-upload.module.ts
│   │   │   │   ├── transferencia.module.ts
│   │   │   │   ├── adenda.module.ts
│   │   │   │   ├── cartaPorteTren.module.ts
│   │   │   │   └── external-api.module.ts
│   │   │   ├── models/                # ~20 modelos Sequelize/TS
│   │   │   │   ├── cupo.model.ts
│   │   │   │   ├── adenda.model.ts
│   │   │   │   ├── transferencia.model.ts
│   │   │   │   ├── cartaPorteTren.model.ts
│   │   │   │   ├── motivos-error.model.ts
│   │   │   │   ├── valores-calidad.model.ts
│   │   │   │   └── ... (otros modelos)
│   │   │   ├── config/                # Sequelize config
│   │   │   ├── guards/
│   │   │   ├── utils/
│   │   │   │   ├── token.middleware.ts  # JWT validation
│   │   │   │   └── logger.interceptor.ts
│   │   │   └── validators/
│   │   ├── migrations/                # Migraciones de DB
│   │   ├── models/                    # Modelos Sequelize para migraciones
│   │   ├── config/
│   │   └── docker/
│   └── frontend/                      # Angular MFE
│       └── src/app/
│           ├── page/
│           │   ├── descargas/
│           │   ├── adenda/
│           │   ├── carta-porte/
│           │   ├── carta-porte-tren/
│           │   ├── centro-descargas/
│           │   └── transferencia/
│           ├── base/
│           ├── components/
│           ├── data/                  # Servicios HTTP al backend
│           ├── domain/                # Lógica de negocio frontend
│           ├── validators/
│           └── remote-entry/
│
├── logistica-app/                     # 🚚 Logística
│   ├── backend/
│   │   └── api/
│   │       ├── README.md
│   │       └── source/                # Yii2 PHP (legacy)
│   │           ├── controllers/       # 15 controladores PHP
│   │           ├── models/
│   │           ├── views/
│   │           ├── migrations/
│   │           ├── config/
│   │           └── composer.json
│   └── frontend/                      # Angular MFE
│       ├── main/                      # ⚠️ Carpeta "main" dentro del MFE (inusual)
│       └── src/app/
│           ├── page/proveedor/
│           ├── services/
│           ├── state/                 # NGXS state
│           ├── models/
│           ├── guards/
│           ├── ux-components/
│           └── remote-entry/
│
├── oferta-app/                        # 💰 Ofertas
│   ├── backend/
│   │   └── api/
│   │       └── source/                # Yii2 PHP (legacy)
│   │           ├── controllers/
│   │           ├── models/
│   │           └── ...
│   └── frontend/
│       └── src/app/
│           ├── page/
│           │   ├── ofertas/
│           │   └── tablero/
│           ├── services/
│           ├── base/
│           ├── shared/
│           └── remote-entry/
│
├── documentacion-app/                 # 📄 Documentación empresarial
│   ├── backend/
│   │   └── api/
│   │       └── source/                # Yii2 PHP (legacy)
│   │           ├── controllers/       # 14 controladores PHP
│   │           ├── models/
│   │           ├── modules/
│   │           ├── console/
│   │           ├── migrations/
│   │           └── ...
│   └── frontend/
│       └── src/app/
│           ├── modules/
│           │   ├── categoria/
│           │   └── centro-documentacion/
│           ├── components/
│           ├── shared/
│           ├── guards/
│           └── remote-entry/
│
├── pedidos-app/                       # 📦 Pedidos (🚧 En desarrollo)
│   ├── backend/                       # NestJS (stub)
│   │   └── src/app/
│   │       ├── app.module.ts
│   │       ├── app.controller.ts
│   │       └── app.service.ts
│   ├── mobile/                        # Ionic Angular
│   └── frontend/                      # Angular MFE
│
├── superapp/                          # 🌐 SuperApp (🚧 En desarrollo)
│   ├── backend/                       # NestJS (stub)
│   │   └── src/app/
│   ├── frontend/                      # Angular MFE
│   └── mobile/                        # Ionic Angular
│
├── shared/                            # 📚 Librerías compartidas
│   ├── data-access-user/              # Estado NGXS del usuario
│   └── frontend/
│       ├── auth/                      # Guards y servicios auth compartidos
│       ├── global-setting/
│       └── ux-ui-components/          # Componentes UI reutilizables
│
├── environments/                      # Variables de entorno Angular
│   ├── environment.ts
│   └── environment.prod.ts
│
├── migrations/                        # Migraciones globales del monorepo
│   └── 20260212175127-insert-motivos-error-adicionales.js
│
├── scripts/                           # Scripts de build y utilidades
│   ├── build-frontend.sh
│   └── create-module.sh
│
├── dockerfiles/                       # Dockerfiles del frontend
│   ├── frontend.base.dockerfile
│   └── nginx.conf
│
└── 000-example-app/                   # Ejemplo/template de nueva app
    ├── backend/
    └── frontend/
```
