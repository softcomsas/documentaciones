# Matriz de Dependencias — Landing Site Muvin

> `✔` = el módulo **fila** depende del módulo **columna**
> `—` = sin dependencia directa

| | **Nginx** | **WordPress** | **MySQL** | **Docker Compose** |
|---|---|---|---|---|
| **Nginx** | — | ✔ | — | — |
| **WordPress** | — | — | ✔ | — |
| **MySQL** | — | — | — | — |
| **Docker Compose** | ✔ | ✔ | ✔ | — |

## Notas al pie

| Celda | Tipo de dependencia | Descripción |
|-------|---------------------|-------------|
| Nginx → WordPress | Llamada de red (FastCGI) + Archivo compartido (volumen) | Nginx reenvía peticiones PHP al proceso FPM de WordPress vía FastCGI en puerto 9000. Además comparten el volumen `/var/www/html` para que Nginx sirva archivos estáticos directamente. |
| WordPress → MySQL | Conexión de base de datos | WordPress se conecta a `db:3306` con credenciales definidas en variables de entorno para toda operación de lectura/escritura de contenido. |
| Docker Compose → Nginx | Orquestación (definición de servicio) | Docker Compose declara imagen, hostname, puertos, volúmenes y red del contenedor Nginx. |
| Docker Compose → WordPress | Orquestación (definición de servicio) | Docker Compose declara imagen, hostname, variables de entorno, volúmenes y orden de arranque (`depends_on: db`). |
| Docker Compose → MySQL | Orquestación (definición de servicio) | Docker Compose declara imagen, hostname, variables de entorno, comando de autenticación y volumen de datos. |
