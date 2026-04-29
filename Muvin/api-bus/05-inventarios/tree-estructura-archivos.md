# Árbol de Archivos — api-bus

> **Última revisión:** 2026-04-29

```
api-bus/
├── docker-compose.yaml          # Orquestación Docker
├── Dockerfile                   # Build imagen PHP + Apache
├── README.md
├── config/
│   └── 000-default.conf         # Configuración Apache VirtualHost
└── source/                      # Código fuente Yii 2
    ├── composer.json            # Dependencias PHP (yii2, swiftmailer, etc.)
    ├── composer.lock
    ├── yii                      # Entry point CLI
    ├── yii.bat
    ├── init / init.bat          # Script de inicialización de entornos
    ├── LICENSE.md
    ├── README.md                # Template Yii 2 (no documentación real)
    ├── requirements.php         # Verificador de requisitos del servidor
    ├── api/                     # Entry point web (index.php)
    │   ├── index-test.php
    │   └── robots.txt
    ├── commands/
    │   └── HelloController.php  # 💀 Comando de ejemplo Yii (no usado)
    ├── config/
    │   ├── main.php             # ⚙️ Configuración principal + módulos + 🔒 credenciales
    │   ├── console.php          # Configuración CLI
    │   ├── params.php           # Parámetros globales
    │   ├── rutas.php            # Rutas URL (vacío actualmente)
    │   └── __autocomplete.php   # Helpers IDE
    ├── controllers/
    │   └── SiteController.php   # Controller básico Yii (error, index)
    ├── environments/            # Configs por entorno (dev/prod)
    ├── exceptions/              # Excepciones personalizadas
    ├── mail/                    # Templates de email
    ├── migrations/
    │   └── m220711_134828_create_agroq_san_miguel_key_table.php
    ├── models/
    │   ├── ContactForm.php      # 💀 Form de contacto (no usado en API)
    │   └── LoginForm.php        # 💀 Form de login web (no usado en API)
    ├── runtime/                 # Logs, caché, sesiones (generado)
    ├── views/                   # Vistas Yii (solo para SiteController/error)
    └── modules/
        ├── common/
        │   ├── Module.php
        │   ├── models/
        │   │   └── User.php     # Identidad JWT
        │   └── components/
        │       ├── BaseCurl.php                    # ⚙️ Cliente HTTP base
        │       └── rest/
        │           ├── ApiRestMuvinController.php  # ⚙️ Controlador base con JWT+roles
        │           ├── ApiRestController.php
        │           ├── ActiveController.php
        │           └── ActiveSearchAction.php
        ├── silohub/
        │   ├── Module.php                          # config: urlBase, OAuth2, passOpenSSL
        │   ├── controllers/
        │   │   └── AlgoritmoController.php         # 10 acciones
        │   └── components/
        │       ├── SiloHub.php                     # Facade → pasamanos
        │       └── LoginSiloHub.php                # OAuth2 singleton
        ├── timbues/
        │   ├── Module.php                          # config: urlBase, passOpenSSL
        │   ├── controllers/
        │   │   └── TimbuesController.php           # 1 acción
        │   └── components/
        │       ├── Timbues.php
        │       └── LoginTimbues.php
        ├── viterra/
        │   ├── Module.php
        │   ├── controllers/
        │   │   └── DestinoController.php           # 2 acciones
        │   ├── forms/
        │   │   └── SituacionTurnos.php
        │   └── models/                             # 12 modelos ActiveRecord
        │       ├── TurnoPuerto.php
        │       ├── HorarioPuerto.php
        │       ├── OrigenDestino.php
        │       ├── Cupo.php / Ventanilla.php
        │       └── ...
        ├── monsanto/
        │   ├── Module.php
        │   └── controllers/
        │       └── LoginController.php             # 1 acción
        │   └── components/
        │       ├── CerealTrackConnect.php
        │       └── Auth.php                        # Auth propio Monsanto
        └── agroquimicos/
            ├── Module.php                          # config: urlBase Zarcam + SanMiguel
            ├── controllers/
            │   └── PedidoController.php            # 2 acciones
            ├── components/
            │   ├── Zarcam.php
            │   └── SanMiguel.php
            ├── models/
            │   └── SanMiguelKey.php               # ActiveRecord agroq_san_miguel_key
            └── useCase/
                └── PedidosById.php                # UseCase: Zarcam + SanMiguel combinados
```
