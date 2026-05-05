# Índice de Archivos de Datos — Config-Deploys Muvin

| Archivo | Ruta | Propósito | Formato | ¿Versionado? | ¿Se regenera? | Notas |
|---------|------|-----------|---------|-------------|---------------|-------|
| `.gitlab-ci.yml` | `/` | Pipeline completo CI/CD | YAML | ✅ Sí | Manual | Archivo más crítico del repo |
| `deploy_back.sh` | `/` | Script manual de deploy API | Bash | ✅ Sí | Manual | Alternativa al pipeline |
| `deploy_front.sh` | `/` | Script manual de deploy FE | Bash | ✅ Sí | Manual | Requiere Node.js en servidor |
| `README.md` | `/` | Instrucciones de variables y pasos | Markdown | ✅ Sí | Manual | |
| `sockets/docker-compose.yml` | `sockets/` | Stack Docker sockets + Redis | YAML | ✅ Sí | Manual | Desplegado via `scp` al servidor |
| `conf/mantenimiento/mantenimiento.conf` | `conf/mantenimiento/` | VirtualHost Apache mantenimiento | Apache config | ✅ Sí | Manual | Desplegado via `scp` antes de cada deploy |
| `.github/workflows/sync.yml` | `.github/workflows/` | GitHub Action de sync | YAML | ✅ Sí | Manual | |
| `versiones/README.md` | `versiones/` | ⚠️ Solo "API DEV / API UAT" | Markdown | ✅ Sí | N/A | 💀 Sin valor útil |

## Archivos generados en tiempo de ejecución (no versionados)

| Archivo/Path | Generado por | Propósito | Notas |
|--------------|-------------|-----------|-------|
| `/opt/backups/data/muinapp_PRE_DEPLOY.gz` | `2-backup_database-*` | Backup MySQL pre-deploy | Se sobreescribe en cada deploy |
| `/opt/deploy/api/` | `1-deploy_api-*` | Staging de archivos API | Directorio temporal; se borra y recrea |
| `/opt/deploy/panel/` | `1-deploy_fe-*` | Staging de archivos FE | Directorio temporal; se borra y recrea |
| `/tmp/mantenimiento.conf` | `1-mant_site-*` | Copia temporal del conf | Se borra en `post_deploy` |

## Variables de entorno (GitLab CI secrets — no en repo)

| Variable | Propósito |
|----------|-----------|
| `DEV_IP`, `DEV_USER`, `DEV_PASS` | Acceso SSH a servidor DEV |
| `CAP_IP`, `CAP_USER`, `CAP_PASS` | Acceso SSH a servidor CAP |
| `UAT_SERVER`, `UAT_USER`, `UAT_PASS` | Acceso SSH a servidor UAT |
| `PRD_IP`, `PRD_USER`, `PRD_PASS` | Acceso SSH a servidor PRD |
| `DEV_DB_IP/USER/PASS` | MySQL DEV |
| `CAP_DB_IP/USER/PASS` | MySQL CAP |
| `UAT_DB_IP/USER/PASS` | MySQL UAT |
| `PRD_DB_IP/USER/PASS` | MySQL PRD |
| `DEPLOY_AMBIENTE` | Ambiente a desplegar |
| `JOB_OK` | Notificación de nueva imagen |
| `DEPLOY_TOKEN` | Token trigger pipeline GitLab |
| `TARGET_URL/USERNAME/TOKEN` | Credenciales sync GitHub→GitLab |
