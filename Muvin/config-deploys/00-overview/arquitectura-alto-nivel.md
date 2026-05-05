# Arquitectura de Alto Nivel — Config-Deploys Muvin

## Diagrama de capas

```mermaid
graph TD
    subgraph Fuentes["Fuentes de código"]
        GH[GitHub\nconfig-deploys]
        GL[GitLab BCR\nconfig-deploys]
        APIPROJ[GitLab\nmuvinapp-new-api]
        FEPROJ[GitLab\nmuvinapp-new-panel]
    end

    subgraph CI["CI/CD - GitLab Runner (tag: muvin)"]
        PIPELINE[.gitlab-ci.yml\n17 stages]
    end

    subgraph REG["Registry"]
        RAPI[muvinapp-new-api:env]
        RFE[muvinapp-new-panel:env]
        RSOCK[sockets:env]
        RMANT[sitio-en-mantenimiento:muvin]
    end

    subgraph Servidores["Servidores destino"]
        SDEV[DEV Server\n$DEV_IP]
        SCAP[CAP Server\n$CAP_IP]
        SUAT[UAT Server\n$UAT_SERVER]
        SPRD[PRD Server\n$PRD_IP]
    end

    subgraph ServidoresDB["Bases de datos"]
        DBDEV[(dev_muvin_app\n$DEV_DB_IP)]
        DBCAP[(stg_muvin_app\n$CAP_DB_IP)]
        DBUAT[(uat_muvin_app)]
        DBPRD[(prd_muvin_app\n$PRD_DB_IP)]
    end

    GH -->|sync.yml push/delete| GL
    APIPROJ -->|imagen construida| RAPI
    FEPROJ -->|imagen construida| RFE
    GL --> PIPELINE
    PIPELINE -->|sshpass + rsync| SDEV
    PIPELINE -->|sshpass + rsync| SCAP
    PIPELINE -->|sshpass + rsync| SUAT
    PIPELINE -->|sshpass + rsync| SPRD
    REG -->|docker pull| SDEV
    REG -->|docker pull| SCAP
    REG -->|docker pull| SUAT
    REG -->|docker pull| SPRD
    SDEV --- DBDEV
    SCAP --- DBCAP
    SUAT --- DBUAT
    SPRD --- DBPRD
```

## Descripción de cada capa

### 📤 Fuentes de código
- **GitHub:** repositorio espejo de `config-deploys`. La sincronización con GitLab es automática via `sync.yml`.
- **GitLab BCR:** repositorio principal donde corre el pipeline. Los proyectos `muvinapp-new-api` y `muvinapp-new-panel` construyen imágenes Docker y las publican en el registry.

### 🔄 CI/CD — GitLab Runner
- Runner registrado con tag `muvin`.
- El pipeline se activa con variables `DEPLOY_AMBIENTE` (para despliegues) o `JOB_OK` (para notificaciones de nueva imagen).
- Usa `sshpass` para conectarse a los servidores destino vía SSH.

### 📦 Docker Registry
- Registry interno de BCR: `registry.bcr.com.ar`.
- Las imágenes se tagean por ambiente: `dev`, `cap`, `uat`, `prd`.

### 🖥️ Servidores destino
- Cada ambiente tiene su propio servidor Linux con Apache2, Docker, PHP y acceso SSH.
- El deploy copia archivos via `rsync` desde un contenedor Docker temporal al path `/var/www/html`.

### 🗄️ Bases de datos
| Ambiente | Base de datos | Variable host |
|----------|--------------|---------------|
| dev | `dev_muvin_app` | `$DEV_DB_IP` |
| cap | `stg_muvin_app` | `$CAP_DB_IP` |
| uat | `uat_muvin_app` | `$UAT_DB_IP` ⚠️ |
| prd | `prd_muvin_app` | `$PRD_DB_IP` |
