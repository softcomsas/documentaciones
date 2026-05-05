# Flujo: Deploy Completo (ejemplo en DEV)

> **Módulos:** [[modulo-gitlab-ci]] → [[modulo-mantenimiento]] → [[modulo-deploy-api]] → [[modulo-deploy-sockets]] → [[modulo-deploy-fe]] → [[modulo-mantenimiento]]

## Diagrama de secuencia

```mermaid
sequenceDiagram
    actor OP as Operador / Pipeline
    participant CI as GitLab CI
    participant SRV as Servidor DEV
    participant AP as Apache2
    participant DB as MySQL DEV
    participant REG as registry.bcr.com.ar

    Note over OP,REG: FASE 1 — PRE DEPLOY
    OP->>CI: Trigger DEPLOY_AMBIENTE=dev
    CI->>SRV: scp mantenimiento.conf
    CI->>SRV: docker pull sitio-en-mantenimiento:muvin
    CI->>SRV: docker run MantenimientoMuvin :8070
    CI->>AP: a2dissite panel + a2ensite mantenimiento
    AP-->>CI: sitio en mantenimiento ✔

    CI->>DB: mysqldump dev_muvin_app → muinapp_PRE_DEPLOY.gz
    DB-->>CI: backup OK
    CI->>CI: validar backup con grep "Dump completed"

    Note over OP,REG: FASE 2 — DEPLOY API
    CI->>REG: docker pull muvinapp-new-api:dev
    REG-->>CI: imagen descargada
    CI->>SRV: docker run muvin-api -v /opt/deploy/api:/sync
    CI->>SRV: docker exec cp /app/. /sync
    CI->>SRV: rsync /opt/deploy/api/ → /var/www/html/api
    CI->>SRV: chown www-data + chmod 777 assets
    CI->>SRV: docker rm muvin-api

    CI->>SRV: php yii connection/db (health check)
    CI->>SRV: php yii connection/assets-all-permissions
    CI->>SRV: php yii migrate --interactive=0
    SRV-->>CI: migraciones OK

    Note over OP,REG: FASE 3 — DEPLOY SOCKETS (manual)
    OP->>CI: Aprobar job manual deploy_socket
    CI->>SRV: scp docker-compose.yml → /opt/sockets
    CI->>SRV: SOCKET_ENV=dev docker-compose up -d

    Note over OP,REG: FASE 4 — DEPLOY FRONTEND
    CI->>REG: docker pull muvinapp-new-panel:dev
    REG-->>CI: imagen descargada
    CI->>SRV: docker run muvin-fe -v /opt/deploy/panel:/sync
    CI->>SRV: limpiar /var/www/html (*.js *.html etc.)
    CI->>SRV: docker exec cp /usr/share/nginx/html/. /sync
    CI->>SRV: rsync /opt/deploy/panel/ → /var/www/html
    CI->>SRV: docker rm muvin-fe

    Note over OP,REG: FASE 5 — POST DEPLOY
    CI->>AP: a2ensite panel + a2dissite mantenimiento
    CI->>SRV: docker rm -f MantenimientoMuvin
    AP-->>CI: sitio restaurado ✔
```

## Duración estimada por fase

| Fase | Duración aprox. | Observaciones |
|------|----------------|---------------|
| Pre-deploy (mantenimiento + backup) | 2-5 min | Depende del tamaño de la DB |
| Deploy API | 3-8 min | Depende del peso de la imagen y velocidad de red |
| Deploy Sockets | 1-3 min | Solo si se aprueba manualmente |
| Deploy Frontend | 2-5 min | Depende del peso de la imagen |
| Post-deploy | < 1 min | |
| **Total** | **8-22 min** | Sin contar tiempo de espera en manuals |

## Riesgo: sitio bloqueado en mantenimiento

> [!danger] Punto de falla crítico
> Si el pipeline falla entre el `pre_deploy` y el `post_deploy`, el sitio queda en mantenimiento indefinidamente. Se requiere intervención manual para restaurarlo:
> ```bash
> sudo a2ensite panel.muvinapp.com
> sudo a2dissite mantenimiento
> sudo systemctl reload apache2
> docker rm -f MantenimientoMuvin
> ```
