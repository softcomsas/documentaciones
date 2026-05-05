# Glosario — Config-Deploys Muvin

| Término | Definición |
|---------|-----------|
| **dev** | Ambiente de Desarrollo. Despliegue automático al hacer push a la rama `main`. |
| **cap** | Ambiente de Capacitación. Equivalente a staging. Se activa manualmente después del deploy a dev. |
| **uat** | Ambiente de User Acceptance Testing. Se activa de forma manual e independiente. |
| **prd** | Ambiente de Producción. Todos los jobs son manuales. |
| **DEPLOY_AMBIENTE** | Variable GitLab CI que determina qué ambiente desplegar (`dev`, `cap`, `uat`, `prd`). |
| **JOB_OK** | Variable GitLab CI usada para notificaciones de nueva imagen: `imagen_api_dev`, `imagen_panel_cap`, `imagen_socket_prd`, etc. Dispara solo el job específico de un componente. |
| **DEPLOY_TOKEN** | Token de trigger de pipeline GitLab. Usado para encadenar despliegues: dev→cap, cap→prd. |
| **sshpass** | Herramienta que permite pasar una contraseña SSH por variable de entorno (`SSHPASS`). Usada para que el runner se conecte a los servidores destino. |
| **Runner muvin** | GitLab Runner registrado con el tag `muvin`. Es el agente que ejecuta los jobs del pipeline. |
| **registry.bcr.com.ar** | Registry privado de Docker de BCR. Almacena todas las imágenes de la plataforma Muvin. |
| **mantenimiento.conf** | VirtualHost Apache que hace proxy hacia el contenedor `MantenimientoMuvin` (puerto 8070). Se activa antes de cada deploy. |
| **MantenimientoMuvin** | Contenedor Docker que sirve la página de mantenimiento. Imagen: `sitio-en-mantenimiento:muvin`. |
| **muvinapp-new-api** | Proyecto GitLab del backend Yii2/PHP de Muvin. Sus imágenes se despliegan en `/var/www/html/api`. |
| **muvinapp-new-panel** | Proyecto GitLab del frontend (Panel) de Muvin. Sus archivos se sirven desde `/var/www/html`. |
| **muvin-api** | Nombre del contenedor Docker temporal creado durante el deploy API para extraer archivos via `/sync`. |
| **muvin-fe** | Nombre del contenedor Docker temporal creado durante el deploy del frontend para extraer archivos via `/sync`. |
| **yii migrate** | Comando CLI de Yii2 que ejecuta las migraciones de base de datos pendientes. |
| **connection/db** | Comando custom de Yii2 que verifica la conexión a la base de datos. Usado como health check post-deploy. |
| **connection/assets-all-permissions** | Comando custom de Yii2 que verifica permisos del directorio `/assets`. |
| **mysqldump** | Herramienta de backup de MySQL. Genera un dump `.gz` en `/opt/backups/data/muinapp_PRE_DEPLOY.gz` antes de cada deploy. |
| **BCR** | Banco de la Ciudad de Rosario (o entidad similar). Organización que opera la plataforma Muvin. |
| **pre_deploy** | Fase del pipeline: modo mantenimiento ON + backup DB + validación backup. |
| **post_deploy** | Fase del pipeline: modo mantenimiento OFF + trigger al siguiente ambiente. |
| **`when: manual`** | Job de GitLab CI que requiere intervención humana para ejecutarse. Todos los jobs de PRD son manuales. |
| **`when: always`** | Job que se ejecuta siempre, independientemente del resultado del job anterior (ej: activar mantenimiento). |
