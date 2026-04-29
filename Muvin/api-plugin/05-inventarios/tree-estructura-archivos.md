# Árbol de Estructura de Archivos

**Proyecto:** plugin-muvin-cupos  
**Raíz:** `api-plugin-main/api-plugin-main/`

```
api-plugin-main/
├── .gitlab-ci.yml
├── docker-compose.yaml
├── Dockerfile
├── cron/
│   ├── plugin_cupos              ← cron job principal (*/15 min)
│   └── plugin_cupos_dummy        ← cron job dummy para testing
└── source/                       ← raíz de la aplicación Yii2
    ├── codeception.yml
    ├── composer.json
    ├── docker-compose.yml
    ├── LICENSE.md
    ├── README.md
    ├── requirements.php
    ├── Vagrantfile               ← legacy, no usar
    ├── yii                       ← entry point consola (Linux)
    ├── yii.bat                   ← entry point consola (Windows)
    ├── assets/
    │   └── AppAsset.php
    ├── Base de datos/
    │   └── Tablas.sql            ← script SQL de referencia
    ├── commands/
    │   ├── ClavesController.php
    │   ├── HelloController.php
    │   ├── LectorController.php   ← yii lector (PRINCIPAL)
    │   ├── ParametrosController.php ← yii parametros (PRINCIPAL)
    │   ├── TestController.php
    │   └── TestExcelController.php
    ├── components/
    │   ├── Auth.php
    │   ├── Db.php
    │   ├── Ejecutor.php           ← orquestador principal
    │   ├── Email.php
    │   ├── Email.php.bak          ← ⚠️ backup en repo
    │   ├── Idle/
    │   ├── JwtValidationData.php
    │   ├── Multimedia/
    │   ├── Plugin/
    │   │   ├── Cupos/             ← procesos por empresa (v1)
    │   │   │   ├── ProcesoAca.php
    │   │   │   ├── ProcesoBunge.php
    │   │   │   ├── ProcesoCofco.php
    │   │   │   ├── ProcesoDreyfus.php
    │   │   │   ├── ProcesoFyo.php
    │   │   │   ├── ProcesoMolinosAgro.php
    │   │   │   ├── ProcesoSamsa.php
    │   │   │   ├── ProcesoDescargasRT.php
    │   │   │   ├── ProcesoDescargasRT2.php
    │   │   │   ├── ServiciosMuvin.php  ← 🔴 creds hardcodeadas
    │   │   │   ├── ServiciosDescargas.php
    │   │   │   ├── LeerPdf.php
    │   │   │   ├── LeerPdfDescargas.php
    │   │   │   ├── LeerExcel.php
    │   │   │   ├── LeerHtmlBungue.php
    │   │   │   ├── LeerHtmlCofco.php
    │   │   │   ├── LeerHtmlMolinosAgro.php
    │   │   │   ├── LeerHtmlSamsa.php
    │   │   │   ├── LeerHtmlTomasHnosFyo.php
    │   │   │   ├── Destinos.php
    │   │   │   ├── Productos.php
    │   │   │   ├── CuitEmpresas.php
    │   │   │   ├── OnccaDestinosProductos.php
    │   │   │   └── Herramientas.php
    │   │   └── hibrido/
    │   │       └── lectorBarcode.php
    │   ├── PluginV2/              ← procesos refactorizados (v2, 2025)
    │   │   ├── BaseProcess.php
    │   │   ├── MailClient.php
    │   │   ├── dto/
    │   │   │   ├── EmailDto.php
    │   │   │   └── ResultParseDto.php
    │   │   ├── parsers/
    │   │   │   ├── DescargaRtParserFile.php
    │   │   │   └── ParseError.php
    │   │   ├── procedures/
    │   │   │   └── DescargaRtProcess.php
    │   │   └── utils/
    │   ├── Rbac/
    │   │   └── DbManager.php
    │   ├── Repositorio.php
    │   └── Tools.php
    ├── config/
    │   └── (archivos de configuración Yii2)
    ├── controllers/
    │   ├── PluginController.php   ← endpoints HTTP
    │   ├── SiteController.php
    │   └── SwaggerController.php
    ├── docs/
    ├── exceptions/
    ├── mail/
    ├── migrations/               ← 75+ migraciones (2021-2025)
    ├── models/
    │   ├── Cliente.php
    │   ├── ClienteConfiguracion.php
    │   ├── Proceso.php
    │   ├── DescargasRt.php
    │   ├── User.php
    │   └── Cupos/
    │       ├── CupoDocumento.php
    │       ├── CupoCodigo.php
    │       └── objCupoMuvin.php
    ├── runtime/
    ├── swagger/
    ├── tests/
    ├── vagrant/
    ├── views/
    └── web/                      ← entry point HTTP (index.php)
```

## Archivos clave

| Archivo | Importancia |
|---|---|
| `commands/LectorController.php` | ⭐ Punto de entrada principal (cron) |
| `commands/ParametrosController.php` | ⭐ Sincronización de catálogos |
| `components/Ejecutor.php` | ⭐ Orquestador central |
| `components/Plugin/Cupos/ServiciosMuvin.php` | 🔴 Cliente Muvin API (creds hardcodeadas) |
| `controllers/PluginController.php` | Endpoints HTTP manuales |
| `components/PluginV2/BaseProcess.php` | ⭐ Base de la nueva arquitectura |
| `cron/plugin_cupos` | ⭐ Scheduler de producción |
| `Dockerfile` | Containerización |

## Métricas del proyecto

| Métrica | Valor |
|---|---|
| Migraciones | ~75 archivos (2021-2025) |
| Procesos por empresa | 9 (7 empresas + 2 variantes RT) |
| Parsers HTML | 5 (uno por formato de empresa) |
| Comandos de consola | 6 |
| Endpoints HTTP | ~15 |
