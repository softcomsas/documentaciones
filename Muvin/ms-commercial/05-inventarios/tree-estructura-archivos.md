# Árbol de Estructura de Archivos

**Última actualización:** 2026-04-27

## Estructura Completa del Proyecto

```
c:\Proyectos\bcr-muvin\ms-commercial\
├─ src/
│  ├─ app.module.ts                    # Módulo raíz
│  ├─ main.ts                          # Bootstrap del microservicio
│  │
│  ├─ config/                          # Configuración
│  │  ├─ environments.ts              # Validación de env vars (Joi)
│  │  ├─ transport.ts                 # Config de Transport TCP
│  │  └─ _index.ts
│  │
│  ├─ core/                            # @Global Services
│  │  ├─ prisma.service.ts            # ORM MySQL
│  │  ├─ repositories/
│  │  │  ├─ contracts.repository.ts   # Acceso a contracts table
│  │  │  ├─ entities.repository.ts    # Acceso a entities table
│  │  │  ├─ products.repository.ts    # Acceso a products table
│  │  │  ├─ keys.repository.ts        # Acceso a keys table
│  │  │  └─ _index.ts
│  │  ├─ services/
│  │  └─ core.module.ts
│  │
│  ├─ modules/                         # Módulos de negocio
│  │  ├─ contracts/
│  │  │  ├─ contracts.module.ts       # Declaración
│  │  │  ├─ contracts.controller.ts   # 7 RPC handlers
│  │  │  └─ contracts.service.ts      # Lógica de negocio
│  │  │
│  │  └─ validation/
│  │     ├─ validation.module.ts      # Declaración
│  │     ├─ validation.controller.ts  # 5 RPC handlers
│  │     └─ validation.service.ts     # Lógica de validación
│  │
│  ├─ contracts/                       # Interfaces inter-microservicios
│  │  ├─ auth/
│  │  │  ├─ contract.ts              # MsAuth namespace
│  │  │  ├─ schema.ts                # Validación Zod
│  │  │  └─ interfaces/
│  │  ├─ commercial/
│  │  │  ├─ contract.ts              # CommercialContracts namespace
│  │  │  ├─ schema.ts                # Validación Zod
│  │  │  └─ interfaces/
│  │  │     ├─ companies.ts
│  │  │     └─ contracts.ts
│  │  ├─ integrations/
│  │  ├─ logs/
│  │  ├─ types.ts
│  │  └─ _index.ts
│  │
│  └─ common/                          # Utilidades compartidas
│     ├─ cmd/                          # MessagePatterns constants
│     │  ├─ constant.ts
│     │  ├─ interface.ts
│     │  ├─ interfaces/
│     │  │  ├─ auth.ts
│     │  │  ├─ commercial.ts
│     │  │  ├─ common.ts
│     │  │  ├─ integrations.ts
│     │  │  ├─ logs.ts
│     │  │  └─ _index.ts
│     │  └─ _index.ts
│     ├─ functions/                   # Helper functions
│     │  ├─ logger.ts                # Logger con colores ANSI
│     │  ├─ api-response.ts          # successResponseFn, errResponseFn
│     │  ├─ identity.ts
│     │  └─ _index.ts
│     └─ interfaces/                  # Tipos compartidos
│        ├─ api-response.ts          # TApi<T>
│        ├─ option.ts                # Option<T>
│        ├─ option-extended.ts
│        ├─ pagination.ts            # IPagination
│        └─ _index.ts
│
├─ prisma/
│  ├─ schema.prisma                  # Definición de tablas y relaciones
│  └─ migrations/                    # Historial de migraciones
│     ├─ migration_lock.toml
│     └─ [timestamp]_init/
│        └─ migration.sql
│
├─ test/                             # Tests
│  ├─ contracts.e2e-spec.ts
│  ├─ validation.e2e-spec.ts
│  └─ jest.config.js
│
├─ .github/
│  └─ workflows/
│     ├─ deploy-dev.yml              # Deploy a dev branch
│     └─ sync-cap.yml                # Sync cap branch
│
├─ docker/
│  └─ docker-compose.yml             # Compose para dev local
│
├─ .env.example                      # Plantilla de env vars
├─ .env                              # Env vars (local, NO commitar)
├─ Dockerfile                        # Imagen Docker
├─ docker-compose.yml                # Compose para desarrollo
├─ nest-cli.json                     # Configuración de NestJS CLI
├─ package.json                      # Dependencias y scripts
├─ package-lock.json
├─ tsconfig.json                     # Configuración de TypeScript
├─ tsconfig.build.json               # Para compilación
├─ jest.config.js                    # Configuración de tests
├─ .eslintrc.js                      # Reglas de linting
├─ .prettierrc                       # Configuración de formato
├─ README.md                         # Instrucciones básicas
└─ docs/                             # DOCUMENTACIÓN (estás aquí)
   ├─ README.md                      # Índice de documentación
   ├─ 00-overview/
   ├─ 01-modulos/
   ├─ 02-funcionalidades/
   ├─ 03-servicios-backend/
   ├─ 04-modelo-de-datos/
   ├─ 05-inventarios/
   ├─ 06-flujos-transversales/
   ├─ 07-operacion-y-despliegue/
   └─ 08-riesgos-y-deuda-tecnica/
```

---

## Tamaño Aproximado del Código

| Carpeta | Archivos | LOC | Descripción |
|---------|----------|-----|------------|
| src/modules | 6 | 1500-2000 | Lógica de negocio |
| src/core | 5 | 600-800 | Acceso a datos |
| src/contracts | 12 | 800-1000 | Interfaces |
| src/common | 10 | 400-600 | Utilitarios |
| src/config | 3 | 100-150 | Configuración |
| prisma | 1 | 200-300 | Schema |
| test | 2+ | 500+ | Tests |
| TOTAL | ~39 | 3500-5000 | |

---

## Documentos Relacionados

- [[cross-module-dependencies|Dependencias Entre Módulos]] - Gráfico de acoplamiento
- [[../01-modulos/_indice-modulos|Módulos NestJS]] - Estructura de módulos

