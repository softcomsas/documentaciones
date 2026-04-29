# Documentación — plugin-muvin-cupos (api-plugin-main)

Plugin backend en PHP/Yii2 que descarga notificaciones de cupos de múltiples cerealeras vía correo electrónico (IMAP), parsea los adjuntos (PDF/HTML/Excel) y carga los cupos en la plataforma Muvin mediante su API REST.

---

## Estructura de la documentación

```
api-plugin-main/
├── README.md                          ← este archivo
├── 00-overview/
│   ├── vision-general.md              ← propósito, stack, contexto
│   ├── arquitectura-general.md        ← diagrama de capas y flujo general
│   ├── decisiones-tecnicas.md         ← ADRs y decisiones de diseño
│   └── glosario.md                    ← términos del dominio
├── 01-modulos/
│   ├── plugin-cupos.md                ← módulo principal de cupos (Plugin/Cupos/)
│   ├── plugin-v2.md                   ← módulo PluginV2 (DescargasRT)
│   ├── ejecutor.md                    ← orquestador Ejecutor.php
│   └── commands.md                    ← comandos de consola (yii lector, yii parametros)
├── 03-servicios-backend/
│   ├── plugin-endpoints.md            ← endpoints HTTP de PluginController
│   └── muvin-api-client.md            ← cliente HTTP hacia Muvin API (ServiciosMuvin)
├── 04-modelo-de-datos/
│   ├── er-diagram.md                  ← diagrama ER de todas las tablas
│   ├── cliente.md
│   ├── cliente-configuracion.md
│   ├── proceso.md
│   ├── cupo-documento.md
│   └── descargas-rt.md
├── 05-inventarios/
│   ├── tree-estructura-archivos.md    ← árbol de directorios
│   ├── inventario-componentes.md
│   ├── inventario-modelos.md
│   ├── inventario-comandos.md
│   ├── inventario-endpoints.md
│   ├── inventario-dependencias.md
│   └── inventario-migraciones.md
├── 06-flujos-transversales/
│   ├── flujo-descarga-cupos.md        ← flujo completo email → parse → Muvin
│   ├── flujo-descargas-rt.md          ← flujo DescargasRT (COE)
│   └── flujo-sincronizacion-catalogos.md
├── 07-operacion-y-despliegue/
│   ├── cron.md
│   ├── docker.md
│   └── configuracion.md
└── 08-riesgos-y-deuda-tecnica/
    ├── riesgos-seguridad.md
    ├── deuda-tecnica.md
    └── mejoras-propuestas.md
```

---

## Stack técnico

| Capa | Tecnología |
|---|---|
| Framework | Yii2 PHP |
| BD | MySQL (via Yii2 ActiveRecord) |
| Email | IMAP (PHP imap extension) |
| PDF parsing | Librería externa (via LeerPdf.php) |
| HTML parsing | DOMDocument / regex |
| Containerización | Docker + docker-compose |
| Scheduler | cron (`*/15 * * * *`) |
| CI/CD | GitLab CI (`.gitlab-ci.yml`) |

---

## Inicio rápido

```bash
# Levantar con Docker
docker-compose up -d

# Correr el proceso completo manualmente
php yii parametros   # sincroniza catálogos desde Muvin API
php yii lector       # descarga mails y procesa cupos
```
