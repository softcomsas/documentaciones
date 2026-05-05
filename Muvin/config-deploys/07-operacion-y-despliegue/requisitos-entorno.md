# Requisitos de Entorno — Config-Deploys Muvin

## Variables GitLab CI/CD (obligatorias)

Configurar en **GitLab → Settings → CI/CD → Variables**:

### Servidores SSH

| Variable | Descripción | Ambiente |
|----------|-------------|---------|
| `DEV_IP` | IP del servidor DEV | dev |
| `DEV_USER` | Usuario SSH servidor DEV | dev |
| `DEV_PASS` | Contraseña SSH servidor DEV | dev |
| `CAP_IP` | IP del servidor CAP | cap |
| `CAP_USER` | Usuario SSH servidor CAP | cap |
| `CAP_PASS` | Contraseña SSH servidor CAP | cap |
| `UAT_SERVER` | IP/hostname del servidor UAT | uat |
| `UAT_USER` | Usuario SSH servidor UAT | uat |
| `UAT_PASS` | Contraseña SSH servidor UAT | uat |
| `PRD_IP` | IP del servidor PRD | prd |
| `PRD_USER` | Usuario SSH servidor PRD | prd |
| `PRD_PASS` | Contraseña SSH servidor PRD | prd |

### Bases de datos

| Variable | Descripción | Ambiente |
|----------|-------------|---------|
| `DEV_DB_IP` | Host MySQL DEV | dev |
| `DEV_DB_USER` | Usuario MySQL DEV | dev |
| `DEV_DB_PASS` | Contraseña MySQL DEV | dev |
| `CAP_DB_IP` | Host MySQL CAP | cap |
| `CAP_DB_USER` | Usuario MySQL CAP | cap |
| `CAP_DB_PASS` | Contraseña MySQL CAP | cap |
| `UAT_DB_IP` | Host MySQL UAT | uat |
| `UAT_DB_USER` | Usuario MySQL UAT | uat |
| `UAT_DB_PASS` | Contraseña MySQL UAT | uat |
| `PRD_DB_IP` | Host MySQL PRD | prd |
| `PRD_DB_USER` | Usuario MySQL PRD | prd |
| `PRD_DB_PASS` | Contraseña MySQL PRD | prd |

### Pipeline

| Variable | Descripción |
|----------|-------------|
| `DEPLOY_TOKEN` | Token de trigger del pipeline GitLab (para encadenar ambientes) |

## Secrets de GitHub Actions

Configurar en **GitHub → Settings → Secrets and variables → Actions**:

| Secret | Descripción |
|--------|-------------|
| `TARGET_URL` | URL del repositorio GitLab destino |
| `TARGET_USERNAME` | Usuario GitLab para sync |
| `TARGET_TOKEN` | Token de acceso personal de GitLab |

## Requisitos en los servidores destino

| Requisito | Descripción |
|-----------|-------------|
| Docker | Instalado y corriendo |
| `docker-compose` (v1 CLI) | Para el stack de sockets |
| Apache2 | Con `mod_proxy` habilitado, `a2enmod proxy proxy_http` |
| PHP + Yii2 CLI | Para migraciones (`php yii migrate`) |
| `mysqldump` | Para backups pre-deploy |
| `rsync` | Para copia de archivos |
| Usuario SSH con sudo | Para `chown`, `chmod`, `cp`, `systemctl reload apache2` sin contraseña |
| Directorio `/opt/backups/data/` | Creado en el servidor |
| Script `/opt/backups/muvinbackupdatabase` | Para deploy manual (`deploy_back.sh`) |
| GitLab Runner | Con tag `muvin` registrado |

## Requisitos de red

| Conexión | Descripción |
|----------|-------------|
| Runner → Servidores (SSH 22) | El runner GitLab debe alcanzar los servidores por SSH |
| Servidores → registry.bcr.com.ar | Para `docker pull` de imágenes |
| Runner → registry.bcr.com.ar | Para verificaciones (si aplica) |
| Runner → gitlab.bcr.com.ar (443) | Para triggers de pipeline via curl |
