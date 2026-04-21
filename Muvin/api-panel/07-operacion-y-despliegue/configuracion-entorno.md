# Configuración del Entorno

> **Última revisión:** 2026-04-21
> **Ver también:** [[despliegue-docker]], [[security-inventory]]

---

## Archivos de configuración

| Archivo | ¿En repo? | Propósito |
|---------|-----------|-----------|
| `backend/config/main.php` | ✅ Sí | Configuración base del backend |
| `backend/config/main-local.php` | ❌ No | Override local (DB, cache, etc.) |
| `common/config/main.php` | ✅ Sí | Configuración base común |
| `common/config/main-local.php` | ❌ No | Override local |
| `params.php` | ⚠️ Sí (verificar) | Credenciales y parámetros |
| `backend/config/cors.php` | ✅ Sí | Orígenes CORS permitidos |
| `backend/config/rutas.php` | ✅ Sí | Rutas URL de la API |
| `backend/config/swagger.php` | ✅ Sí | Config Swagger/OpenAPI |

---

## Parámetros requeridos en params.php

```php
<?php
return [
    // Base de datos (también en main-local.php)
    'adminEmail'       => 'admin@muvinapp.com',
    
    // Microservicio AFIP
    'user_micro_afip'  => '<usuario>',
    'pass_micro_afip'  => '<password>',
    'url_micro_afip'   => '<https://ms.afip.muvinapp.com/>',
    
    // Infobip SMS
    'infobip_url'      => '<https://api.infobip.com/sms/2/text/advanced>',
    'infobip_auth'     => 'Basic <base64token>',
    
    // Microservicio RBAC (⚠️ rotar si es '123456')
    's-roles-permisos.token' => '<token-seguro>',
    's-roles-permisos.url'   => '<https://ms.rbac.muvinapp.com/>',
    
    // Bus Integración (ERP)
    'bus_integracion_url'    => '<https://bus.muvinapp.com/>',
    'bus_integracion_token'  => '<token>',
    
    // WhatsApp / Bot
    'whatsapp_token'   => '<token>',
    'whatsapp_url'     => '<https://api.whatsapp.muvinapp.com/>',
    
    // JWT
    'jwt_secret'       => '<clave-secreta-larga>',
    'jwt_ttl'          => 86400,  // segundos
];
```

> [!danger] Seguridad
> El parámetro `'s-roles-permisos.token' => '123456'` debe ser rotado inmediatamente. Ver [[security-inventory]] SEC-001.

---

## Configuración de base de datos (main-local.php)

```php
// backend/config/main-local.php
return [
    'components' => [
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn'   => 'mysql:host=<host>;dbname=<database>',
            'username' => '<user>',
            'password' => '<password>',
            'charset'  => 'utf8mb4',
        ],
    ],
];
```

---

## Configuración CORS

**Archivo:** `backend/config/cors.php`

```php
return [
    'origins' => [
        // ⚠️ Eliminar el '*' — permite cualquier origen
        '*',
        'https://dev.muvinapp.com/',
        'https://dev.muvinapp.com/lite/',
        'https://dev.muvinapp.com/descargas/',
        'https://dev.muvinapp.com/logistica/',
        '172.16.9.175:4505',  // Fachada interna
    ]
];
```

---

## Variables de entorno Docker

Además de los volúmenes, se pueden pasar variables via `environment` en `docker-compose.yml`:

```yaml
environment:
  - APP_ENV=prod
  - YII_ENV=prod
  - YII_DEBUG=0
```

---

## Estructura de configuración Yii2

```
config/
├── main.php          ← Config base (siempre cargada)
├── main-local.php    ← Override local (no en repo)
├── bootstrap.php     ← Bootstrap de la app
├── cors.php          ← Config CORS
├── rutas.php         ← URL rules / routing
├── swagger.php       ← Config Swagger prod
└── swagger-local.php ← Config Swagger local
```

La app carga: `main.php` + `main-local.php` en ese orden. `main-local.php` sobreescribe cualquier clave de `main.php`.
