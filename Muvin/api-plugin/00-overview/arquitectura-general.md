# Arquitectura General — plugin-muvin-cupos

## Diagrama de capas

```
┌─────────────────────────────────────────────────────────┐
│                    CRON / HTTP                          │
│         (*/15 min: yii lector  |  GET /plugin)         │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│               Capa de Entrada                           │
│  ┌────────────────────┐  ┌────────────────────────────┐ │
│  │  LectorController  │  │    PluginController        │ │
│  │  (yii lector)      │  │    (GET /plugin/*)         │ │
│  └────────┬───────────┘  └──────────┬─────────────────┘ │
└───────────┼──────────────────────────┼──────────────────┘
            │                          │
┌───────────▼──────────────────────────▼──────────────────┐
│               Orquestador                               │
│                  Ejecutor.php                           │
│   (itera Cliente → ClienteConfiguracion → Proceso)     │
│   call_user_func($proceso->metodo, $clienteConfig)      │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│           Procesos por Empresa (Plugin/Cupos/)          │
│  ProcesoAca | ProcesoBunge | ProcesoCofco               │
│  ProcesoDreyfus | ProcesoSamsa | ProcesoMolinosAgro     │
│  ProcesoFyo | ProcesoDescargasRT | ProcesoDescargasRT2  │
└──────┬──────────────────────────────────────────┬───────┘
       │                                          │
┌──────▼──────────┐                    ┌──────────▼───────┐
│  Parsers        │                    │  ServiciosMuvin  │
│  LeerPdf        │                    │  (HTTP → Muvin   │
│  LeerHtml*      │                    │   API REST)      │
│  LeerExcel      │                    └──────────────────┘
└──────┬──────────┘
       │
┌──────▼──────────────────────────────────────────────────┐
│               Modelos ActiveRecord (MySQL)              │
│  Cliente | ClienteConfiguracion | Proceso               │
│  CupoDocumento | CupoCodigo | DescargasRt               │
└─────────────────────────────────────────────────────────┘
```

## Patrón Multi-Tenant

El plugin usa una arquitectura de 3 niveles para el multi-tenancy:

```
Cliente (empresa cliente de Muvin)
  ├── user_muvin / password  → credenciales para Muvin API
  └── ClienteConfiguracion (cuenta de email)
        ├── usermail / pwdmail / srvmail → credenciales IMAP
        └── Proceso (proceso asociado)
              └── metodo → string callable (ej: "ProcesoAca::DescargarMails")
```

Esto permite que un mismo cliente tenga múltiples cuentas de correo y múltiples procesos activos.

## PluginV2 — Arquitectura Mejorada

`PluginV2/` es una refactorización más estructurada para los procesos de DescargasRT:

```
BaseProcess (abstract)
  └── DescargaRtProcess
        ├── DescargaRtParserFile (parser)
        ├── EmailDto (DTO entrada)
        └── ResultParseDto (DTO salida)
```

Diferencias respecto a Plugin/Cupos/:
- Usa clases abstractas con herencia formal
- Introduce DTOs explícitos
- Separa parsers en clases dedicadas

## Componentes de infraestructura

| Componente | Clase | Función |
|---|---|---|
| Auth | `Auth.php` | Login/logout de usuarios del panel web |
| Db | `Db.php` | Wrapper de transacciones DB |
| Email | `Email.php` | Envío de notificaciones por correo |
| Tools | `Tools.php` | Utilidades: `saveDatos()` para logging |
| JwtValidationData | `JwtValidationData.php` | Generación y validación de JWT |
| Repositorio | `Repositorio.php` | Base ActiveRecord con `listar()`, `getOne()` |

## Flujo de datos — Cupos

```
IMAP Server
    │  (descargar emails no leídos)
    ▼
adjunto (PDF/HTML/Excel)
    │  (LeerPdf / LeerHtml* / LeerExcel)
    ▼
Array de datos parseados
    │  (validar y guardar)
    ▼
cupo_documento (MySQL) ← estado: PENDIENTE
    │  (CargarMuvin → ServiciosMuvin::cargarCupo())
    ▼
Muvin API REST
    │  (respuesta)
    ▼
cupo_documento ← estado: PROCESADO / ERROR
```
