# Deuda Técnica — Landing Site Muvin

> Lista priorizada por **Impacto** (en seguridad/operación) y **Esfuerzo** (para resolverlo).

## Matriz de deuda técnica

| # | Deuda | Impacto | Esfuerzo | Prioridad |
|---|-------|---------|----------|-----------|
| DT-01 | Actualizar WordPress a 6.x | 🔴 Alto | 🟡 Medio | **P1** |
| DT-02 | Actualizar MySQL a 8.x | 🔴 Alto | 🟡 Medio | **P1** |
| DT-03 | Actualizar Nginx a 1.24+ | 🔴 Alto | 🟢 Bajo | **P1** |
| DT-04 | Agregar restricción IP a `/wp-admin/` | 🔴 Alto | 🟢 Bajo | **P1** |
| DT-05 | Implementar secrets management (reemplazar `.env` plano) | 🔴 Alto | 🟡 Medio | **P1** |
| DT-06 | Activar rate limiting en `wp-login.php` | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-07 | Eliminar o deshabilitar XML-RPC si no se usa | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-08 | Resolver estado de `nginx.conf.new` (descartar o reemplazar) | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-09 | Agregar `autoindex off` a `nginx.conf.new` si se va a usar | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-10 | Remover `all-in-one-wp-migration.6.77.zip` del repositorio Git | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-11 | Agregar healthcheck a servicio `db` en docker-compose.yml | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-12 | Documentar e implementar backup automático de MySQL | 🟡 Medio | 🟡 Medio | **P2** |
| DT-13 | Mejorar header HSTS con `includeSubDomains; preload` | 🟢 Bajo | 🟢 Bajo | **P3** |
| DT-14 | Verificar y documentar plugins instalados en producción | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-15 | Externalizar IPs de whitelist (actualmente hardcodeadas en nginx.conf) | 🟡 Medio | 🟡 Medio | **P3** |

## Descripción detallada de deudas P1

### DT-01 — Actualizar WordPress a 6.x
WordPress 5.3.2 no recibe parches de seguridad. Procedimiento: backup con all-in-one-wp-migration → actualizar imagen en `docker-compose.yml` a `wordpress:6.x-fpm-alpine` → `docker compose up -d` → verificar compatibilidad de plugins/temas.

### DT-02 — Actualizar MySQL a 8.x
MySQL 5.7 EOL desde Oct 2023. Procedimiento: dump de la DB actual → actualizar imagen a `mysql:8.0` → cambiar `--default-authentication-plugin` por configuración moderna → restaurar dump → verificar WordPress.

### DT-03 — Actualizar Nginx a 1.24+
Cambio de imagen en `docker-compose.yml`. Bajo riesgo de incompatibilidad ya que la configuración es estándar.

### DT-04 — Agregar restricción IP a `/wp-admin/`
Agregar en `nginx.conf`:
```nginx
location ~* /wp-admin/ {
    allow 172.16.0.0/24;
    allow 172.26.0.0/24;
    allow 172.25.0.0/24;
    allow 192.168.12.0/22;
    include ./conf.d/allowip.ip;
    deny all;
}
```

### DT-05 — Secrets management
Reemplazar archivo `.env` con credenciales en texto plano por Docker Secrets (para Compose) o integración con Vault/AWS SSM.
