# Visión General — plugin-muvin-cupos

## ¿Qué es?

`plugin-muvin-cupos` es un plugin backend en PHP/Yii2 que actúa como **puente de integración** entre las cerealeras del mercado argentino y la plataforma Muvin. Su función es:

1. **Descargar** notificaciones de cupos enviadas por las cerealeras vía correo electrónico (IMAP)
2. **Parsear** los adjuntos (PDF, HTML, Excel) para extraer datos de cartas porte y cupos
3. **Cargar** los cupos procesados en la plataforma Muvin mediante su API REST
4. **Sincronizar** catálogos locales (productos, destinos, CUITs) desde Muvin

## Contexto de negocio

En el mercado granario argentino, las cerealeras (ACA, Bunge, Cofco, Dreyfus, Samsa, MolinosAgro, Tomas Hnos/Fyo) emiten **cupos** a los productores mediante correo electrónico con adjuntos en formato PDF o HTML. Estos cupos contienen cartas porte (CP) que deben ser registradas en el sistema Muvin.

Este plugin automatiza ese proceso, eliminando la carga manual.

## Cerealeras soportadas

| ID proceso | Empresa | Formato | Método |
|---|---|---|---|
| 1 | ACA | PDF | `ProcesoAca::DescargarMails` |
| 2 | Dreyfus | HTML | `ProcesoDreyfus::DescargarMails` |
| 3 | Bunge | HTML | `ProcesoBunge::DescargarMails` |
| 4 | SAMSA | HTML | `ProcesoSamsa::DescargarMails` |
| 5 | Cofco | HTML | `ProcesoCofco::DescargarMails` |
| 6 | MolinosAgro | HTML | `ProcesoMolinosAgro::DescargarMails` |
| — | Tomas Hnos/Fyo | HTML | `ProcesoFyo::DescargarMails` |
| — | Tomas Hnos/Dreyfus | HTML | `ProcesoDreyfus::DescargarMails` |
| — | Tomas Hnos/Cofco | HTML | `ProcesoCofco::DescargarMails` |

## Stack técnico

| Capa | Tecnología |
|---|---|
| Framework | Yii2 PHP |
| Base de datos | MySQL |
| Acceso a emails | PHP IMAP extension |
| Parsing PDF | Clase `LeerPdf.php` (librería externa) |
| Parsing HTML | `LeerHtml*.php` por empresa (DOMDocument/regex) |
| Parsing Excel | `LeerExcel.php` |
| API Muvin | HTTP REST via `ServiciosMuvin.php` |
| Contenedor | Docker + docker-compose |
| Scheduler | cron Linux (`*/15 * * * *`) |
| CI/CD | GitLab CI |

## Tipo de aplicación

- **Sin frontend** — exclusivamente backend/batch
- **Multi-tenant**: cada `Cliente` tiene sus propias credenciales de correo y configuraciones de proceso
- **Sin estado HTTP**: el proceso es iniciado por cron o por llamada HTTP manual a `GET /plugin`

## Flujo de alto nivel

```
cron (*/15 min)
  └─► yii parametros → sincroniza catálogos desde Muvin API
  └─► yii lector → Ejecutor::correrProcesos()
        └─► por cada Cliente activo
              └─► por cada ClienteConfiguracion activa
                    └─► por cada Proceso activo
                          └─► call_user_func($proceso->metodo, $clienteConfig)
                                └─► DescargarMails() → parsear adjunto → CargarMuvin()
```

## Repositorio

- **Ruta en workspace:** `c:\Users\rodol\Downloads\api-plugin-main\api-plugin-main\`
- **Código fuente Yii2:** `source/`
- **Cron job:** `cron/plugin_cupos`
- **Docker:** `Dockerfile` + `docker-compose.yaml`
