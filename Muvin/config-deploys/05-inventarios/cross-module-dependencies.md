# Dependencias entre Módulos — Config-Deploys Muvin

## Diagrama de dependencias

```mermaid
graph TD
    GH[🐙 GitHub Repo] -->|push event| SYNC[modulo-github-sync\nsync.yml]
    SYNC -->|replica ramas| GL[GitLab Repo BCR]
    GL -->|dispara| CI[modulo-gitlab-ci\n.gitlab-ci.yml]

    CI -->|pre_deploy| MANT[modulo-mantenimiento\nmantenimiento.conf]
    CI -->|deploy_api| DAPI[modulo-deploy-api]
    CI -->|deploy_socket| DSOCK[modulo-deploy-sockets\nsockets/docker-compose.yml]
    CI -->|deploy_fe| DFE[modulo-deploy-fe]
    CI -->|post_deploy| MANT

    MANT -->|sshpass SSH| APACHESRV[Apache2 en servidor\na2ensite/a2dissite]
    DAPI -->|sshpass + docker + rsync| SRVSRV[Servidor destino\n/var/www/html/api]
    DFE -->|sshpass + docker + rsync| SRVSRV2[Servidor destino\n/var/www/html]
    DSOCK -->|sshpass + docker-compose| SOCKHOST[Servidor destino\n/opt/sockets]

    SCRIPTS[modulo-scripts-manuales\ndeploy_back.sh / deploy_front.sh] -.->|alternativa manual| SRVSRV
    SCRIPTS -.->|alternativa manual| SRVSRV2

    REG[registry.bcr.com.ar] -->|docker pull| DAPI
    REG -->|docker pull| DFE
    REG -->|docker pull| DSOCK
    REG -->|docker pull| MANT
```

## Descripción de dependencias

| Origen | Destino | Tipo | Descripción |
|--------|---------|------|-------------|
| GitHub | modulo-github-sync | Evento (push/delete) | Un push a `main`/`master`/`Produccion` en GitHub dispara la sincronización |
| modulo-github-sync | GitLab BCR | Replicación Git | El workflow copia las ramas al repositorio GitLab |
| GitLab BCR | modulo-gitlab-ci | Trigger de pipeline | Un pipeline se inicia con variables `DEPLOY_AMBIENTE` o `JOB_OK` |
| modulo-gitlab-ci | modulo-mantenimiento | Invocación de etapa | pre/post_deploy activan y desactivan el modo mantenimiento |
| modulo-gitlab-ci | modulo-deploy-api | Invocación de etapa | La etapa `deploy_api_*` ejecuta el deploy del backend |
| modulo-gitlab-ci | modulo-deploy-sockets | Invocación de etapa | La etapa `deploy_socket_*` ejecuta el deploy de sockets |
| modulo-gitlab-ci | modulo-deploy-fe | Invocación de etapa | La etapa `deploy_fe_*` ejecuta el deploy del frontend |
| modulo-deploy-api | registry.bcr.com.ar | Pull de imagen Docker | Descarga `muvinapp-new-api:{env}` |
| modulo-deploy-fe | registry.bcr.com.ar | Pull de imagen Docker | Descarga `muvinapp-new-panel:{env}` |
| modulo-deploy-sockets | registry.bcr.com.ar | Pull de imagen Docker | Descarga `sockets:{env}` |
| modulo-scripts-manuales | Servidor destino | SSH + git + rsync | Alternativa manual al pipeline; sin Docker |
