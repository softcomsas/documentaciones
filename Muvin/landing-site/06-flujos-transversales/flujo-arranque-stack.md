# Flujo: Arranque del Stack Docker Compose

> **Módulos:** [[modulo-docker-compose]] → [[modulo-mysql]] → [[modulo-wordpress]] → [[modulo-nginx]]

## Diagrama de arranque

```mermaid
sequenceDiagram
    actor OP as Operador
    participant DC as Docker Compose
    participant DB as MySQL (db)
    participant WP as WordPress (wordpress)
    participant NGX as Nginx (webserver)

    OP->>DC: docker compose up -d
    DC->>DB: Iniciar contenedor db
    DB->>DB: Inicializar /var/lib/mysql\nCrear DB 'wordpress'\nAplicar mysql_native_password
    DB-->>DC: Contenedor db UP (⚠️ sin healthcheck)
    DC->>WP: Iniciar contenedor wordpress\n(depends_on: db)
    WP->>WP: Verificar wp-config.php\nConectar a db:3306
    WP-->>DC: Contenedor wordpress UP
    DC->>NGX: Iniciar contenedor webserver\n(depends_on: wordpress)
    NGX->>NGX: Cargar nginx.conf\nEscuchar en :8081
    NGX-->>DC: Contenedor webserver UP
    DC-->>OP: Stack completo activo\nPuerto 8088 disponible
```

## Riesgo: race condition en arranque

> [!warning] Race condition MySQL / WordPress
> `depends_on: db` en Docker Compose espera que el **contenedor** esté corriendo, no que MySQL esté listo para aceptar conexiones. Si MySQL tarda en inicializarse (primera vez, volumen vacío), WordPress puede fallar al conectarse y crashear. Docker Compose lo reiniciará (política `unless-stopped`) hasta que MySQL esté disponible.
>
> **Solución recomendada:** Agregar `healthcheck` al servicio `db` con `mysqladmin ping`.

## Prerequisitos en el host

```
/opt/landingpage/dbdata/       # debe existir (o Docker lo crea vacío)
/opt/landingpage/wordpress/    # debe existir con WordPress instalado o imagen lo inicializa
.env                           # debe estar presente con las 3 variables de credenciales
```
