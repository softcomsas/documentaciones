# Índice de Módulos — Config-Deploys Muvin

| # | Módulo | Descripción | Criticidad | Archivo |
|---|--------|-------------|------------|---------|
| 1 | Pipeline GitLab CI/CD | `.gitlab-ci.yml` — orquesta todos los despliegues | 🔴 Alta | [[modulo-gitlab-ci]] |
| 2 | Deploy API | Extrae imagen y despliega backend Yii2 | 🔴 Alta | [[modulo-deploy-api]] |
| 3 | Deploy Frontend | Extrae imagen y despliega archivos del panel | 🟡 Media | [[modulo-deploy-fe]] |
| 4 | Deploy Sockets | Docker Compose con socket-muvin + Redis | 🟡 Media | [[modulo-deploy-sockets]] |
| 5 | Modo Mantenimiento | Apache VirtualHost + contenedor Docker | 🟡 Media | [[modulo-mantenimiento]] |
| 6 | Scripts manuales | `deploy_back.sh` y `deploy_front.sh` | 🟢 Baja | [[modulo-scripts-manuales]] |
| 7 | Sync GitHub→GitLab | `sync.yml` — espejo automático de ramas | 🟢 Baja | [[modulo-github-sync]] |
