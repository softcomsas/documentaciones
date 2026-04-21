# Árbol de Estructura de Archivos

> **Generado:** 2026-04-21
> **Herramienta:** revisión manual del workspace
> **Proyecto:** `muvin-ms-worker`

---

```
worker/
├── docker/
│   ├── Dockerfile                  # Build multi-stage (builder + producción, Node 20 Alpine, usuario no-root)
│   └── docker-compose.yml          # Levanta MySQL local para desarrollo; el servicio principal está comentado ⚠️
│
├── src/
│   ├── main.ts                     # Bootstrap del proceso: NestFactory.create + app.init() — SIN listen HTTP
│   ├── module.ts                   # AppModule raíz: registra BullModule (Redis), colas y providers
│   │
│   ├── config/
│   │   ├── _index.ts               # Barrel de exports del módulo config
│   │   ├── environments.ts         # Validación Joi de variables de entorno (HOST, PORT para Redis)
│   │   └── queues.ts               # Definición tipada de colas y nombres de procesos Bull
│   │
│   ├── common/
│   │   ├── _index.ts               # Barrel raíz de common (re-exporta functions, interfaces, cmd)
│   │   │
│   │   ├── functions/
│   │   │   ├── _index.ts           # Barrel de funciones utilitarias
│   │   │   ├── logger.ts           # Función LOG con colores ANSI usando NestJS Logger
│   │   │   ├── identity.ts         # Función identidad genérica IDENTITY<T>(x) => x
│   │   │   └── api-response.ts     # Helpers successResponseFn / errResponseFn 💀 (no usados en worker)
│   │   │
│   │   ├── interfaces/
│   │   │   ├── _index.ts           # Barrel de interfaces
│   │   │   ├── api-response.ts     # Tipos TApi, TApiSuccessResponse, TApiErrorResponse 💀 (no usados)
│   │   │   ├── option.ts           # Interface IOption<T> 💀 (no usada)
│   │   │   ├── option-extended.ts  # Interface IOptionExtended<T> 💀 (no usada)
│   │   │   ├── pagination.ts       # Interface IPagination 💀 (no usada)
│   │   │   └── jobs/
│   │   │       ├── _index.ts       # Barrel de interfaces de jobs
│   │   │       ├── email/
│   │   │       │   ├── _index.ts   # Barrel email jobs
│   │   │       │   └── pdf.ts      # Interface IJobEmailPdf — contrato del job email.pdf
│   │   │       └── internal/
│   │   │           ├── _index.ts   # Barrel internal jobs
│   │   │           └── notification.ts # Interface IJobInternalNotification 💀 (cola sin procesador)
│   │   │
│   │   └── cmd/
│   │       ├── _index.ts           # Barrel de CMDs
│   │       ├── interface.ts        # Interface ICMDS — tipado del objeto de comandos de microservicio
│   │       ├── constant.ts         # Constante CMDS con todos los message patterns del ecosistema Muvin
│   │       └── interfaces/
│   │           ├── _index.ts       # Barrel de interfaces CMD
│   │           ├── auth.ts         # ICMDAuth — comandos de autenticación
│   │           ├── commercial.ts   # ICMDCommercial — comandos de contratos
│   │           ├── common.ts       # ICMDCreate, ICMDUpdate — interfaces base
│   │           ├── integrations.ts # ICMDIntegrations — comandos de integraciones de email
│   │           └── logs.ts         # ICMDLogs — comandos de logs legacy
│   │
│   ├── modules/
│   │   └── email/
│   │       ├── processor.ts        # EmailProcessor: único procesador activo — escucha cola "email", proceso "email.pdf"
│   │       └── functions/
│   │           ├── _index.ts       # Barrel de funciones del módulo email
│   │           └── rt.ts           # extractPartsFn, getAttachmentsFn, extractAndValidateTextFn + ITransferencia
│   │
│   └── services/
│       ├── _index.ts               # Barrel de servicios
│       └── pdf-parser.ts           # PdfParserService: convierte base64 → Buffer → Uint8Array → TextResult
│
├── eslint.config.mjs               # Configuración ESLint 9 (flat config)
├── nest-cli.json                   # Configuración NestJS CLI
├── package.json                    # Dependencias y scripts npm
├── tsconfig.json                   # Configuración TypeScript principal
├── tsconfig.build.json             # Configuración TypeScript para build de producción
└── tsconfig.paths.json             # Path aliases: @common, @config, @services, @contracts ⚠️
```

---

## Notas por carpeta

| Carpeta | Contenido | Observaciones |
|---------|-----------|---------------|
| `docker/` | Infraestructura de contenedores | `docker-compose.yml` tiene el servicio principal comentado |
| `src/config/` | Configuración del entorno | Solo valida `HOST` y `PORT` (Redis). Sin validación de credenciales Gmail |
| `src/common/functions/` | Utilidades compartidas | `api-response.ts` e `identity.ts` no tienen uso en este worker |
| `src/common/interfaces/` | Contratos de tipos | `option.ts`, `option-extended.ts`, `pagination.ts` son dead code en este contexto |
| `src/common/cmd/` | Message patterns del ecosistema | `CMDS` no se usa actualmente en el worker; es código compartido del monorepo |
| `src/modules/email/` | Único módulo funcional activo | Contiene el procesador Bull y toda la lógica de extracción |
| `src/modules/` | Módulos de procesamiento | La cola `internal` está definida pero **no tiene procesador implementado** 🔴 |
| `src/services/` | Servicios inyectables | Solo `PdfParserService` activo |

## Path Aliases (tsconfig.paths.json)

| Alias | Resolución | Estado |
|-------|-----------|--------|
| `@common` | `src/common/_index` | ✅ Activo |
| `@config` | `src/config/_index` | ✅ Activo |
| `@services` | `src/services/_index` | ✅ Activo |
| `@contracts` | `src/contracts/_index` | ⚠️ Directorio `src/contracts/` **no existe** |
