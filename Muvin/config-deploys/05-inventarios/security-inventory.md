# Inventario de Seguridad — Config-Deploys Muvin

> **Fecha de auditoría:** 2026-05-05
> **Fuentes analizadas:** `.gitlab-ci.yml`, `deploy_back.sh`, `deploy_front.sh`, `sockets/docker-compose.yml`, `conf/mantenimiento/mantenimiento.conf`, `.github/workflows/sync.yml`

---

## 🔒 Variables con credenciales (GitLab CI secrets)

| Variable | Descripción | Ambiente |
|----------|-------------|---------|
| `DEV_PASS` | Contraseña SSH servidor dev | dev |
| `CAP_PASS` | Contraseña SSH servidor cap | cap |
| `UAT_PASS` | Contraseña SSH servidor uat | uat |
| `PRD_PASS` | Contraseña SSH servidor prd | prd |
| `DEV_DB_USER`, `DEV_DB_PASS` | Credenciales MySQL dev | dev |
| `CAP_DB_USER`, `CAP_DB_PASS` | Credenciales MySQL cap | cap |
| `UAT_DB_USER`, `UAT_DB_PASS` | Credenciales MySQL uat | uat |
| `PRD_DB_USER`, `PRD_DB_PASS` | Credenciales MySQL prd | prd |
| `DEPLOY_TOKEN` | Token de trigger de pipeline GitLab | todos |
| `TARGET_TOKEN` | Token GitHub→GitLab sync | — |

---

## 🔴 Hallazgos críticos

### SEC-001 — sshpass: contraseña SSH en variable de entorno de proceso
| Severidad | 🔴 Alta |
|-----------|---------|
| Descripción | El pipeline usa `sshpass -e` que expone la contraseña en la variable de entorno `SSHPASS`. Cualquier proceso del mismo host con acceso a `/proc` puede leerla. Además, las contraseñas quedan en los logs del runner. |
| Ubicación | `.gitlab-ci.yml` — todos los jobs SSH (líneas ~40, 55, 90, etc.) |
| Recomendación | Reemplazar `sshpass` + contraseña por **autenticación SSH con par de claves**. La clave privada se almacena en GitLab CI/CD secrets como `SSH_PRIVATE_KEY`. |

### SEC-002 — Contraseña MySQL en variable de entorno del proceso mysqldump
| Severidad | 🔴 Alta |
|-----------|---------|
| Descripción | El comando `mysqldump --password=${DEV_DB_PASS}` expone la contraseña en la línea de comandos del proceso, visible en `ps aux` y en logs del sistema. |
| Ubicación | `.gitlab-ci.yml` — jobs `2-backup_database-*` |
| Recomendación | Usar archivo de opciones MySQL (`~/.my.cnf`) o `--defaults-extra-file` para no exponer la contraseña en la línea de comandos. |

### SEC-003 — `chmod 777` en directorio de assets de la API
| Severidad | 🔴 Alta |
|-----------|---------|
| Descripción | El deploy de API ejecuta `sudo chmod 777 /var/www/html/api/backend/assets`. Permisos 777 en un directorio web permiten a cualquier proceso escribir archivos en él, incluyendo webshells. |
| Ubicación | `.gitlab-ci.yml` — jobs `1-deploy_api-*` |
| Recomendación | Usar `chmod 775` o `chmod 755` según el caso. El usuario `www-data` no necesita permisos de escritura para mundo. |

---

## 🟡 Hallazgos de severidad media

### SEC-004 — GitHub Actions sin hash de versión (supply chain risk)
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | `actions/checkout@v2` y `wangchucheng/git-repo-sync@v0.1.0` se referencian por tag, no por SHA commit. Un atacante que tome control de esas acciones puede ejecutar código arbitrario en el runner. |
| Ubicación | `.github/workflows/sync.yml` |
| Recomendación | Usar `actions/checkout@<SHA>` con el hash completo del commit. |

### SEC-005 — `mantenimiento.conf` sin ServerName
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | El VirtualHost de mantenimiento no tiene `ServerName`. Apache lo puede activar como catch-all, interceptando peticiones de otros dominios en el mismo servidor. |
| Ubicación | `conf/mantenimiento/mantenimiento.conf` |
| Recomendación | Agregar `ServerName panel.muvinapp.com` al VirtualHost. |

### SEC-006 — `versiones/README.md` con información residual
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | El archivo solo contiene "API DEV\nAPI UAT". Puede ser un residuo de notas internas que no debería estar versionado. |
| Ubicación | `versiones/README.md` |
| Recomendación | Eliminar o completar con información útil. |

### SEC-007 — `DEPLOY_TOKEN` y project ID hardcodeado en pipeline
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | El trigger de pipeline usa la URL `https://gitlab.bcr.com.ar/api/v4/projects/200/trigger/pipeline` con el project ID hardcodeado. Si el proyecto se mueve o el ID cambia, el encadenamiento falla silenciosamente. |
| Ubicación | `.gitlab-ci.yml` — jobs `2-start-deploy_to_cap` y `2-start-deploy_to_prd` |
| Recomendación | Externalizar el project ID como variable GitLab CI. |

---

## 🟢 Controles de seguridad positivos identificados

| Control | Descripción | Ubicación |
|---------|-------------|-----------|
| ✅ Credenciales en GitLab secrets | Ninguna contraseña versionada en el repositorio | `.gitlab-ci.yml` usa variables `$VAR` |
| ✅ Backup pre-deploy | Se genera dump MySQL antes de cada deploy | `2-backup_database-*` |
| ✅ Validación del backup | Se verifica integridad del dump con grep antes de continuar | `3-validate_backup_datebase-*` |
| ✅ Modo mantenimiento | El sitio se protege durante el deploy | `1-mant_site-*` |
| ✅ `set -e` en scripts Bash | Los scripts manuales fallan inmediatamente en error | `deploy_back.sh`, `deploy_front.sh` |
| ✅ Todo PRD es manual | Ningún job de producción se ejecuta automáticamente | `.gitlab-ci.yml` — `when: manual` en prd |
| ✅ Imagen Docker como fuente de verdad | Los archivos provienen de imágenes versionadas, no de builds locales | `.gitlab-ci.yml` deploy jobs |
| ✅ Health check de DB post-deploy | `php yii connection/db` verifica conectividad antes de migrar | `3-validate_unit_test-*` |
