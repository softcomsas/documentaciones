# Inventario de Modelos

**Ruta base:** `source/models/`

## Modelos principales

| Modelo | Tabla | Descripción |
|---|---|---|
| `Cliente` | `cliente` | Empresa cliente del plugin |
| `ClienteConfiguracion` | `cliente_configuracion` | Cuenta IMAP de un cliente |
| `Proceso` | `proceso` | Proceso/método a ejecutar para un cliente |
| `DescargasRt` | `descargas_rt` | Descarga RT identificada por COE |
| `User` | `user` | Usuario del panel web (autenticación JWT) |

## Modelos Cupos/

| Modelo | Tabla | Descripción |
|---|---|---|
| `CupoDocumento` | `cupo_documento` | Cupo parseado pendiente de carga |
| `CupoCodigo` | `cupo_codigo` | Códigos de carta porte de un cupo |
| `objCupoMuvin` | — | DTO de cupo para enviar a Muvin API |

## Modelos de catálogos (inferidos de migraciones)

| Tabla | Descripción |
|---|---|
| `productos` | Catálogo de granos/productos |
| `destinos` | Catálogo de destinos/plantas |
| `cuit_empresas` | CUITs de cerealeras |
| `oncca_destino_producto` | Combos destino-producto ONCCA |

## Modelos de notificaciones (inferidos de migraciones)

| Tabla | Descripción |
|---|---|
| `cupo_mail_notif` | Notificaciones enviadas por email para un cupo |
| `email_template` | Templates HTML para emails de notificación |
| `descargas_rt_notif` | Notificaciones para DescargasRT |

## Campos de estado

Todos los modelos transaccionales usan el patrón de tres estados:

| Estado | Descripción |
|---|---|
| `PENDIENTE` | Registrado, esperando procesamiento |
| `PROCESADO` | Procesado exitosamente |
| `ERROR` | Falló el procesamiento |

## Clase base Repositorio

Todos los modelos extienden `Repositorio` (que extiende `ActiveRecord`), que proporciona:

```php
class Repositorio extends ActiveRecord {
    static public function listar($conditions = []) { ... }
    static public function getOne($conditions = []) { ... }
}
```
