# Visión General — Config-Deploys Muvin

> **Estado:** Producción activa
> **Antigüedad aproximada:** ~2021-2022 (según referencias internas)

## ¿Qué hace el sistema?

`config-deploys` es el repositorio central de **CI/CD y operaciones de despliegue** del ecosistema Muvin en BCR. Concentra:

1. El pipeline completo de GitLab CI/CD (`.gitlab-ci.yml`, 1057 líneas) que automatiza el despliegue de la API backend, el frontend panel y el servicio de sockets a los ambientes de Desarrollo, Capacitación, UAT y Producción.
2. Scripts manuales de deploy (`deploy_front.sh`, `deploy_back.sh`) para situaciones de emergencia o fuera del pipeline.
3. Configuración del modo de mantenimiento (Apache vhost proxy).
4. Docker Compose del servicio de sockets.
5. Workflow de sincronización GitHub ↔ GitLab.

## ¿A quién sirve?

- **Equipo de DevOps / infraestructura:** opera los despliegues.
- **Desarrolladores backend/frontend:** sus commits en los proyectos `muvinapp-new-api` y `muvinapp-new-panel` desencadenan builds de imágenes Docker que este pipeline despliega.
- **Equipo de QA:** los ambientes CAP y UAT son sus entornos de validación.

## Ambientes gestionados

| Ambiente | Código | Tipo | Activación |
|----------|--------|------|-----------|
| Desarrollo | `dev` | Automático en push a `main` | `DEPLOY_AMBIENTE=dev` |
| Capacitación | `cap` | Manual (post-dev) | `DEPLOY_AMBIENTE=cap` |
| UAT | `uat` | Manual independiente | `DEPLOY_AMBIENTE=uat` |
| Producción | `prd` | Todo manual | `DEPLOY_AMBIENTE=prd` |

## Componentes desplegados por este pipeline

| Componente | Imagen Docker | Path en servidor |
|------------|--------------|-----------------|
| API Backend (Yii2/PHP) | `registry.bcr.com.ar/muvinapp/muvinapp-new-api:{env}` | `/var/www/html/api` |
| Frontend Panel | `registry.bcr.com.ar/muvinapp/muvinapp-new-panel:{env}` | `/var/www/html` |
| Servicio de Sockets | `registry.bcr.com.ar/muvinapp/sockets:{env}` | `/opt/sockets` (Docker Compose) |
| Sitio de Mantenimiento | `registry.bcr.com.ar/docker/sitio-en-mantenimiento:muvin` | Puerto `8070` (Docker) |

## Números relevantes

| Métrica | Valor |
|---------|-------|
| Líneas en `.gitlab-ci.yml` | 1057 |
| Stages del pipeline | 17 (general + 4 grupos × 4 etapas) |
| Ambientes gestionados | 4 (dev, cap, uat, prd) |
| Variables GitLab requeridas | 18+ |
| Scripts manuales | 2 |
| Archivos versionados | 8 |
