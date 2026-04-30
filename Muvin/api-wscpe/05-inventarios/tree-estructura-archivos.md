# Árbol de Estructura de Archivos — api-wscpe

> [[README]]

## Estructura del proyecto

```
api-wscpe/
├── README.md
├── .gitignore
└── source/
    ├── composer.json               # Dependencias PHP
    ├── composer.lock
    ├── yii                         # CLI entry point (Unix)
    ├── yii.bat                     # CLI entry point (Windows)
    ├── docker-compose.yml          # Orquestación Docker (dev)
    ├── Afip_res/                   # Certificados X.509 temporales en disco 🔴
    │   ├── cert.key                # (generado en runtime)
    │   └── key.pem                 # (generado en runtime)
    ├── Afip_TA/                    # Tickets de Acceso cacheados en disco
    ├── commands/                   # Comandos CLI Yii2
    ├── components/
    │   ├── Afip.php                # Cliente WSAA (obtener TA de AFIP)
    │   └── Afipws.php              # Wrapper SOAP WSCPE
    ├── config/
    │   ├── web.php                 # Config principal: rutas, CORS, beforeRequest
    │   ├── params.php              # Parámetros: ENTORNO_WS (TEST/PROD)
    │   ├── console.php             # Config CLI
    │   ├── swagger.php             # Config Swagger UI
    │   ├── test.php                # Config tests
    │   └── __autocomplete.php      # Helper IDE
    ├── controllers/
    │   ├── LoginController.php     # POST /login/login
    │   ├── CpeController.php       # POST /cpe
    │   ├── CertificadoController.php  # POST /certificado
    │   ├── UserController.php      # POST /user/create-user
    │   ├── SwaggerController.php   # GET /swagger
    │   ├── DefaultController.php
    │   └── SiteController.php
    ├── migrations/
    │   ├── m230725_232038_create_users_table.php
    │   ├── m230725_233021_create_query_cache_table.php
    │   ├── m230901_061922_certificate_table_create.php
    │   ├── m230925_092932_alter_query_cache_add_cuit_consulta.php
    │   └── m230926_141806_alter_query_cache_alter_ctg.php
    ├── models/
    │   ├── User.php                # AR: tabla user + IdentityInterface
    │   ├── Certificado.php         # AR: tabla certificado
    │   ├── QueryCache.php          # AR: tabla query_cache + lógica de caché/AFIP
    │   ├── LoginForm.php           # Form: validación de login
    │   ├── SignupForm.php          # Form: creación de usuario
    │   └── ContactForm.php         # (no usado en producción)
    ├── swagger/                    # Archivos generados de Swagger
    ├── views/                      # Vistas Yii2 (minimal — API)
    ├── web/
    │   └── index.php               # Entry point HTTP
    └── tests/                      # Tests Codeception
```

## Estadísticas

| Métrica | Valor |
|---------|-------|
| Controllers | 7 (5 activos) |
| Modelos AR | 3 |
| Componentes AFIP | 2 |
| Migraciones | 5 |
| Endpoints REST | 5 |
| Tablas BD | 3 |
