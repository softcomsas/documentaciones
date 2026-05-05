# Funcionalidad: Backup de Base de Datos Pre-Deploy

## Descripción

Antes de cada deploy, el pipeline ejecuta un dump completo de la base de datos MySQL del ambiente correspondiente. El backup se guarda en el servidor destino como archivo `.sql.gz` con timestamp.

## Comando ejecutado

```bash
sshpass -p $DEV_PASS ssh $DEV_USER@$DEV_IP \
  "mysqldump -u $DEV_DB_USER --password=$DEV_DB_PASS -h $DEV_DB_IP \
   --default-character-set=utf8 dev_muvin_app \
   | gzip > /opt/backups/dev_muvin_app_$(date +%Y%m%d%H%M%S).sql.gz"
```

> [!warning] Deuda técnica
> La contraseña `--password=` es visible en `ps aux` durante la ejecución. Ver [[deuda-tecnica]] ítem DT-03 para la corrección.

## Ubicación de los backups

| Ambiente | Ruta en servidor |
|----------|-----------------|
| dev | `/opt/backups/dev_muvin_app_*.sql.gz` |
| cap | `/opt/backups/cap_muvin_app_*.sql.gz` |
| uat | `/opt/backups/uat_muvin_app_*.sql.gz` |
| prd | `/opt/backups/prd_muvin_app_*.sql.gz` |

## Posición en el pipeline

Ocupa el **stage 2** (`2-backup_database`), ejecutado **después** de descargar la imagen y **antes** de activar el modo mantenimiento. Garantiza que siempre hay un punto de restore previo al cambio.

```
1-images → [2-backup_database] → 3-maintenance_on → 4-deploy → ...
```

## Limitaciones conocidas

- No hay rotación de backups implementada en el pipeline — los archivos acumulan indefinidamente.
- El script externo `/opt/backups/muvinbackupdatabase` referenciado en el README no está versionado en este repo.
- No hay verificación de integridad del dump (sin `--single-transaction` ni checksum).

## Referencias

- [[modulo-deploy-api]]
- [[deuda-tecnica]] — DT-03
- [[hotspots]]
