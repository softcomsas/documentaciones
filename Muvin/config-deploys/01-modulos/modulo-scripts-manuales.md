# Módulo: Scripts Manuales de Deploy

> **Archivos:** `deploy_back.sh`, `deploy_front.sh`
> **Criticidad:** 🟢 Baja
> **Estado:** Disponibles (uso en emergencias o fuera del pipeline)

## Propósito

Scripts Bash alternativos al pipeline GitLab CI. Permiten realizar despliegues de forma manual directamente en el servidor, sin pasar por CI/CD. Útiles en situaciones de emergencia, acceso limitado a GitLab o necesidad de deploy rápido.

---

## `deploy_back.sh` — Deploy manual de API

**Path de trabajo:** `/var/www/html/api`
**Mecanismo:** git pull directo (no usa Docker)

```mermaid
flowchart TD
    A([./deploy_back.sh -b rama]) --> B{¿BRANCH vacío?}
    B -->|Sí| ERR[Error y exit]
    B -->|No| C[cd /var/www/html/api]
    C --> D[git checkout -f && git fetch]
    D --> E[git checkout BRANCH && git pull]
    E --> F[php yii connection/db\nHealth check DB]
    F --> G{¿DB conectada?}
    G -->|No| STOP[Detiene deploy]
    G -->|Sí| H[php yii connection/assets-all-permissions]
    H --> I{¿Permisos OK?}
    I -->|No| STOP2[Detiene deploy]
    I -->|Sí| J[sh /opt/backups/muvinbackupdatabase\nBackup DB]
    J --> K[php yii connection/db\nVerificación final]
    K --> L[php yii migrate --interactive=0]
    L --> M([Deploy API completado])
```

**Uso:**
```bash
./deploy_back.sh -b cap
./deploy_back.sh -b Produccion
```

> [!warning] Diferencia con el pipeline
> Este script trabaja con git pull directo sobre `/var/www/html/api`, sin usar imágenes Docker. Requiere que el repositorio esté clonado en esa ruta en el servidor. En el pipeline, los archivos provienen de una imagen Docker. Los dos métodos pueden resultar en versiones distintas si no se sincroniza el tag Docker con el branch.

---

## `deploy_front.sh` — Deploy manual de Frontend

**Path del repo:** `/opt/deploy/muvinapp-new-panel`
**Path destino:** `/var/www/html`
**Mecanismo:** git pull + npm build + rsync

```mermaid
flowchart TD
    A([./deploy_front.sh -b rama]) --> B{¿BRANCH vacío?}
    B -->|Sí| ERR[Error y exit]
    B -->|No| C[cd /opt/deploy/muvinapp-new-panel]
    C --> D[git checkout -f && git fetch]
    D --> E[git checkout BRANCH && git pull]
    E --> F[export NODE_OPTIONS=--max_old_space_size=8096]
    F --> G[npm ci]
    G --> H[npm run build]
    H --> I[Limpiar /var/www/html\n*.svg *.js *.html etc.]
    I --> J[rsync -avz dist/ /var/www/html]
    J --> K([Deploy FE completado])
```

**Uso:**
```bash
./deploy_front.sh -b cap
./deploy_front.sh -b Produccion
```

## Riesgos y deuda técnica

- ⚠️ **`deploy_back.sh` modifica `/var/www/html/api` directamente** — sin staging intermedio. Si el build falla a mitad, la aplicación queda en estado inconsistente.
- ⚠️ **`deploy_front.sh` requiere Node.js en el servidor** — para compilar el frontend. En el pipeline normal, la compilación ocurre durante el build de la imagen Docker, no en el servidor destino.
- 🟡 **`NODE_OPTIONS=--max_old_space_size=8096`** — necesita 8GB de heap para el build del frontend. Indica una aplicación frontend grande o con configuración de memoria no optimizada.
- ⚠️ **`/opt/backups/muvinbackupdatabase`** — referencia a un script externo no versionado en este repositorio. Si no existe en el servidor, el deploy se interrumpe.

## Archivos fuente relevantes

- `deploy_back.sh`
- `deploy_front.sh`
