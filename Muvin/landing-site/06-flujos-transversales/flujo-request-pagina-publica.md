# Flujo: Solicitud de Página Pública

> **Módulos:** [[modulo-nginx]] → [[modulo-wordpress]] → [[modulo-mysql]]
> **Tipo:** Request HTTP sincrónico

## Diagrama de secuencia

```mermaid
sequenceDiagram
    actor U as Usuario / Navegador
    participant N as Nginx :8088
    participant W as WordPress FPM :9000
    participant D as MySQL :3306

    U->>N: GET /alguna-pagina (HTTP)
    N->>N: Verificar método (GET ✔)
    N->>N: Verificar ruta (no bloqueada ✔)
    N->>N: Agregar headers de seguridad al response
    alt Archivo estático (.css, .js, .jpg)
        N-->>U: 200 OK + archivo desde /var/www/html
    else Archivo PHP
        N->>W: FastCGI /index.php (con params)
        W->>D: SELECT wp_posts, wp_options, etc.
        D-->>W: Datos del contenido
        W-->>N: HTML renderizado
        N-->>U: 200 OK + HTML
    end
```

## Flujo alternativo — Ruta no encontrada

```mermaid
flowchart TD
    A([GET /ruta-inexistente]) --> B[Nginx: try_files]
    B --> C{¿Archivo existe?}
    C -->|No| D[Reenvía a /index.php]
    D --> E[WordPress: 404 template]
    E --> F([Response 404 con página WP])
```
