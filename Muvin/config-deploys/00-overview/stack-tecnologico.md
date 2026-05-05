# Stack Tecnológico — Config-Deploys Muvin

| Tecnología | Versión | Propósito | Soporte vendor | Riesgo |
|------------|---------|-----------|----------------|--------|
| GitLab CI/CD | ⚠️ Pendiente de verificar (BCR self-hosted) | Orquestación del pipeline completo | 🟢 Vigente | 🟢 Bajo |
| GitHub Actions | `actions/checkout@v2`, `wangchucheng/git-repo-sync@v0.1.0` | Sincronización GitHub→GitLab | 🟡 `@v2` desactualizado | 🟡 Medio |
| Docker | ⚠️ Pendiente de verificar | Pull/run de imágenes en servidores destino | 🟢 Vigente | 🟢 Bajo |
| Docker Compose | v3 (sintaxis) | Sockets + Redis | 🟢 Vigente | 🟢 Bajo |
| sshpass | ⚠️ Pendiente de verificar | Conexión SSH con contraseña desde pipeline | 🟡 Herramienta legacy/insegura | 🔴 Alto |
| rsync | Sistema | Copia de archivos a servidores | 🟢 Vigente | 🟢 Bajo |
| Apache2 | ⚠️ Pendiente de verificar | Servidor web en destinos; VirtualHost mantenimiento | 🟢 Vigente | 🟢 Bajo |
| Bash | — | Scripts manuales `deploy_front.sh`, `deploy_back.sh` | 🟢 Vigente | 🟢 Bajo |
| PHP Yii2 (CLI) | ⚠️ Pendiente de verificar | Migraciones de DB (`php yii migrate`) y health checks | 🟢 Vigente | 🟢 Bajo |
| Redis | `7.0.4-alpine` | Backend de sockets | 🟢 Vigente | 🟢 Bajo |
| npm | ⚠️ Pendiente de verificar | Build del frontend en script manual | 🟢 Vigente | 🟢 Bajo |

## Notas de riesgo

> [!danger] sshpass — autenticación SSH con contraseña
> El pipeline usa `sshpass` para conectarse a los servidores via SSH con contraseña plana. Esta práctica es considerada insegura ya que la contraseña queda expuesta en la variable de entorno del proceso. La alternativa recomendada es autenticación por clave SSH almacenada en GitLab CI/CD secrets.

> [!warning] actions/checkout@v2 y git-repo-sync@v0.1.0
> Las GitHub Actions usan versiones antiguas sin hash fijo. Riesgo de supply chain attack si los tags son reutilizados por el proveedor.
