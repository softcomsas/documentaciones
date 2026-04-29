# Configuración del Plugin

## Archivos de configuración Yii2

**Ruta:** `source/config/`

Yii2 usa archivos PHP para la configuración. Los archivos típicos son:

| Archivo | Descripción |
|---|---|
| `config/web.php` | Configuración de la aplicación web (URL rules, componentes) |
| `config/console.php` | Configuración de la aplicación de consola |
| `config/db.php` | Configuración de la conexión a BD |
| `config/params.php` | Parámetros globales de la aplicación |

## Configuración de base de datos

```php
// config/db.php
return [
    'class' => 'yii\db\Connection',
    'dsn' => 'mysql:host=' . getenv('DB_HOST') . ';dbname=' . getenv('DB_NAME'),
    'username' => getenv('DB_USER'),
    'password' => getenv('DB_PASS'),
    'charset' => 'utf8',
];
```

## Configuración de la aplicación web

```php
// config/web.php — fragmento típico
return [
    'id' => 'plugin-muvin-cupos',
    'basePath' => dirname(__DIR__),
    'components' => [
        'db' => require __DIR__ . '/db.php',
        'user' => [
            'identityClass' => 'app\models\User',
            'enableAutoLogin' => false,
        ],
        'urlManager' => [
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules' => [
                'GET plugin' => 'plugin/index',
                'GET plugin/<action>' => 'plugin/<action>',
                // ...
            ],
        ],
    ],
];
```

## Variables de entorno

El plugin utiliza las siguientes variables de entorno (configuradas en Docker):

| Variable | Descripción | Ejemplo |
|---|---|---|
| `DB_HOST` | Host MySQL | `db` (nombre del servicio Docker) |
| `DB_PORT` | Puerto MySQL | `3306` |
| `DB_NAME` | Nombre de la BD | `plugin_cupos` |
| `DB_USER` | Usuario MySQL | `plugin_user` |
| `DB_PASS` | Password MySQL | `*****` |

## Configuración hardcodeada (a migrar)

Las siguientes configuraciones están hardcodeadas en el código fuente y **deben migrarse a variables de entorno**:

| Valor hardcodeado | Archivo | Variable de entorno recomendada |
|---|---|---|
| CUIT usuario Muvin: `30700869918` | `ServiciosMuvin.php` | `MUVIN_USER` |
| Password Muvin: `Expo2019!` | `ServiciosMuvin.php` | `MUVIN_PASS` |
| URL Muvin API: `https://revision1.muvinapp.com/api/` | `ServiciosMuvin.php` | `MUVIN_URL` |
| ID clienteConf Tomas Hnos/Cofco: `20` | `PluginController.php` | Migrar a BD |
| ID clienteConf Tomas Hnos/Dreyfus: `24` | `PluginController.php` | Migrar a BD |
| ID clienteConf Fyo: `26` | `PluginController.php` | Migrar a BD |

## Clave de encriptación

La clave AES usada para encriptar los passwords de `Cliente.password` debe estar en la configuración de la aplicación (en `params.php` o como variable de entorno). Verificar `config/params.php` para el parámetro correspondiente.

## Alta de nuevos clientes

Actualmente, agregar un nuevo cliente requiere ejecutar migraciones de BD manualmente:

1. Insertar en `cliente` (nombre, cuit, user_muvin, password encriptado)
2. Insertar en `proceso` (nombre, metodo, cliente_id, activo=1)
3. Insertar en `cliente_configuracion` (usermail, pwdmail, srvmail, cliente_id, proceso_id, activo=1)
4. Insertar en `email_template` (template de notificación)
5. Insertar en `cupo_mail_notif` (configuración de notificaciones)

Ver el patrón de migraciones en `inventario-migraciones.md`.
