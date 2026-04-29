# Inventario de Componentes

**Ruta base:** `source/components/`

## Componentes de infraestructura

| Componente | Archivo | Descripción |
|---|---|---|
| `Auth` | `Auth.php` | Login/logout de usuarios del panel. Incluye bypass de password con `'SuperAdmin'` ⚠️ |
| `Db` | `Db.php` | Wrapper de transacciones DB: `Db::transaction(fn)` |
| `Ejecutor` | `Ejecutor.php` | Orquestador principal: itera clientes → configs → procesos |
| `Email` | `Email.php` | Envío de emails de notificación |
| `JwtValidationData` | `JwtValidationData.php` | Generación y validación de tokens JWT |
| `Repositorio` | `Repositorio.php` | Clase base ActiveRecord con `listar()`, `getOne()` |
| `Tools` | `Tools.php` | Utilidades varias: `saveDatos()` para logging de datos |

## Componentes Plugin/Cupos/ (v1)

### Procesos por empresa

| Componente | Empresa | Formato |
|---|---|---|
| `ProcesoAca` | ACA | PDF |
| `ProcesoBunge` | Bunge | HTML |
| `ProcesoCofco` | Cofco + Tomas Hnos/Cofco | HTML |
| `ProcesoDreyfus` | Dreyfus + Tomas Hnos/Dreyfus | HTML |
| `ProcesoFyo` | Tomas Hnos/Fyo | HTML |
| `ProcesoMolinosAgro` | MolinosAgro | HTML |
| `ProcesoSamsa` | SAMSA | HTML |
| `ProcesoDescargasRT` | RT v1 | Archivo RT |
| `ProcesoDescargasRT2` | RT v2 | Archivo RT |

### Parsers

| Componente | Archivo | Uso |
|---|---|---|
| `LeerPdf` | `LeerPdf.php` | Parseo de PDFs de cupos (ACA) |
| `LeerPdfDescargas` | `LeerPdfDescargas.php` | Parseo de PDFs para descargas |
| `LeerExcel` | `LeerExcel.php` | Parseo de archivos Excel |
| `LeerHtmlBungue` | `LeerHtmlBungue.php` | Parser HTML Bunge |
| `LeerHtmlCofco` | `LeerHtmlCofco.php` | Parser HTML Cofco |
| `LeerHtmlMolinosAgro` | `LeerHtmlMolinosAgro.php` | Parser HTML MolinosAgro |
| `LeerHtmlSamsa` | `LeerHtmlSamsa.php` | Parser HTML SAMSA |
| `LeerHtmlTomasHnosFyo` | `LeerHtmlTomasHnosFyo.php` | Parser HTML Tomas Hnos/Fyo |

### Servicios externos

| Componente | Archivo | Descripción |
|---|---|---|
| `ServiciosMuvin` | `ServiciosMuvin.php` | 🔴 Cliente HTTP a Muvin API (creds hardcodeadas) |
| `ServiciosDescargas` | `ServiciosDescargas.php` | Cliente HTTP para DescargasRT |

### Sincronización de catálogos

| Componente | Tabla destino | Fuente |
|---|---|---|
| `Productos` | `productos` | Muvin API |
| `Destinos` | `destinos` | Muvin API |
| `CuitEmpresas` | `cuit_empresas` | Muvin API |
| `OnccaDestinosProductos` | `oncca_destino_producto` | Muvin API |

### Utilidades

| Componente | Descripción |
|---|---|
| `Herramientas` | Manejo de caracteres especiales, funciones de apoyo |

## Componentes PluginV2/ (v2, 2025)

| Componente | Descripción |
|---|---|
| `BaseProcess` | Clase abstracta base para todos los procesos v2 |
| `MailClient` | Cliente IMAP encapsulado |
| `DescargaRtProcess` | Proceso concreto para DescargasRT |
| `DescargaRtParserFile` | Parser de archivos RT |
| `ParseError` | Clase de error de parseo |
| `EmailDto` | DTO que representa un email |
| `ResultParseDto` | DTO que representa el resultado del parseo |

## Otros componentes

| Componente | Descripción |
|---|---|
| `Rbac/DbManager` | Gestión de roles y permisos (RBAC) |
| `Idle/` | Manejo de sesiones idle |
| `Multimedia/` | Manejo de archivos multimedia |
| `hibrido/lectorBarcode` | Lector de códigos de barras (legacy) |
