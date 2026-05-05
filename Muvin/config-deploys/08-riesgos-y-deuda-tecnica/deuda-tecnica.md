# Deuda Técnica — Config-Deploys Muvin

> Lista priorizada por **Impacto** (seguridad/operación) y **Esfuerzo** (para resolverlo).

## Matriz de deuda técnica

| # | Deuda | Impacto | Esfuerzo | Prioridad |
|---|-------|---------|----------|-----------|
| DT-01 | Reemplazar `sshpass` + contraseña por autenticación SSH con clave | 🔴 Alto | 🟡 Medio | **P1** |
| DT-02 | Corregir `chmod 777` → `chmod 775` en directorio `assets` | 🔴 Alto | 🟢 Bajo | **P1** |
| DT-03 | Usar `--defaults-extra-file` en `mysqldump` (ocultar contraseña) | 🔴 Alto | 🟢 Bajo | **P1** |
| DT-04 | Refactorizar pipeline con `extends` o templates (eliminar repetición) | 🟡 Medio | 🔴 Alto | **P2** |
| DT-05 | Reemplazar jobs de validación `whoami` por smoke tests HTTP reales | 🟡 Medio | 🟡 Medio | **P2** |
| DT-06 | Restaurar/corregir bloque `only:` en jobs de sockets | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-07 | Externalizar project ID `200` como variable GitLab | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-08 | Actualizar GitHub Actions a versiones con hash SHA | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-09 | Agregar `ServerName` al VirtualHost de mantenimiento | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-10 | Agregar mecanismo de rollback automático si pipeline falla en medio | 🟡 Medio | 🔴 Alto | **P3** |
| DT-11 | Agregar gate de calidad (test real) antes de trigger DEV→CAP | 🟡 Medio | 🟡 Medio | **P3** |
| DT-12 | Reemplazar `docker-compose` (v1) por `docker compose` (v2) en scripts | 🟢 Bajo | 🟢 Bajo | **P3** |
| DT-13 | Eliminar `versiones/README.md` o completarlo | 🟢 Bajo | 🟢 Bajo | **P3** |
| DT-14 | Documentar el script `/opt/backups/muvinbackupdatabase` (no está en repo) | 🟡 Medio | 🟢 Bajo | **P2** |

## Descripción detallada de deudas P1

### DT-01 — Reemplazar sshpass por clave SSH
```yaml
# Configuración en GitLab CI/CD:
# Variable: SSH_PRIVATE_KEY (tipo File)

before_script:
  - eval $(ssh-agent -s)
  - echo "$SSH_PRIVATE_KEY" | ssh-add -
  - mkdir -p ~/.ssh && chmod 700 ~/.ssh
  - echo "$SSH_KNOWN_HOSTS" >> ~/.ssh/known_hosts

# Uso en jobs:
- ssh $DEV_USER@$DEV_IP "comando"
```
Elimina las variables `*_PASS` de SSH y el binario `sshpass`.

### DT-02 — Corregir permisos de assets
```bash
# Cambiar en .gitlab-ci.yml:
# ANTES:
sudo chmod 777 /var/www/html/api/backend/assets
# DESPUÉS:
sudo chmod 775 /var/www/html/api/backend/assets
sudo chown -R www-data:www-data /var/www/html/api/backend/assets
```

### DT-03 — Ocultar contraseña en mysqldump
```bash
# Crear archivo de opciones en el servidor destino:
# /home/$USER/.my.cnf con permisos 600:
[mysqldump]
user=DEV_DB_USER
password=DEV_DB_PASS
host=DEV_DB_IP

# Uso en pipeline:
mysqldump --defaults-extra-file=/home/$DEV_USER/.my.cnf --default-character-set=utf8 dev_muvin_app | gzip > ...
```
