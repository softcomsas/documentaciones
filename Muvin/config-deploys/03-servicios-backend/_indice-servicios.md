# Índice de Servicios Externos — Config-Deploys Muvin

> Config-deploys no expone una API propia. En su lugar, **orquesta y despliega** servicios externos del ecosistema Muvin. Esta sección documenta esos servicios como referencias de integración.

## Servicios gestionados por el pipeline

| Servicio | Tipo | Imagen / Origen | Puerto |
|----------|------|-----------------|--------|
| [[servicio-api-backend]] | API PHP/Yii2 | `registry.bcr.com.ar/muvinapp/muvinapp-new-api:{env}` | 80/443 |
| [[servicio-frontend-panel]] | SPA / Static files | `registry.bcr.com.ar/muvinapp/muvinapp-new-panel:{env}` | 80/443 |
| [[servicio-sockets]] | Node.js + Redis | `registry.bcr.com.ar/muvinapp/sockets:{env}` | 5000 |
| [[servicio-mantenimiento]] | Static HTTP | `registry.bcr.com.ar/docker/sitio-en-mantenimiento:muvin` | 8070 |
| [[servicio-registry-bcr]] | Registry Docker | `registry.bcr.com.ar` | 443 |

## Servicios de infraestructura usados

| Servicio | Rol en el pipeline |
|----------|-------------------|
| GitLab CI/CD | Motor de ejecución del pipeline |
| GitHub Actions | Sincronización de espejo del repositorio |
| Servidores Linux (x4) | Destinos de deploy vía SSH + rsync |
| MySQL (x4) | Base de datos de cada ambiente |
| Redis 7.0.4 | Broker de mensajes para sockets |

## Notas

> [!info]
> Las imágenes Docker son generadas por los pipelines de los repositorios fuente (`muvinapp-new-api`, `muvinapp-new-panel`, `sockets`) y **publicadas en el registry**. Este repo solo las **consume y despliega**.

## Referencias

- [[_indice-modulos]]
- [[stack-tecnologico]]
