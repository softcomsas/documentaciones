# Módulo: common

> **Ruta/Namespace:** `source/modules/common/`
> **Criticidad:** 🟢 Soporte
> **Estado:** Activo

## Propósito

Módulo de infraestructura compartida. Provee las clases base que usan todos los demás módulos: cliente HTTP cURL, controlador REST base con autenticación JWT y gestión de roles.

## Componentes que expone

| Componente | Descripción |
|---|---|
| `BaseCurl` | Cliente HTTP via cURL con headers, timeout y SSL configurables |
| `ApiRestMuvinController` | Controlador REST base con JWT Bearer, CORS y helpers de roles |
| `ApiRestController` | Controlador REST alternativo (⚠️ uso no verificado en módulos actuales) |
| `ActiveController` | Extensión de `yii\rest\ActiveController` |
| `ActiveSearchAction` | Acción de búsqueda reutilizable |

## ApiRestMuvinController — Roles soportados

| Propiedad | Rol |
|---|---|
| `_administrador` | ROL_ADMIN |
| `_centro` | ROL_CENTRO |
| `_destino` | ROL_DESTINO |
| `_transportista` | ROL_TRANSPORTISTA |
| `_cargador` | ROL_CARGADOR |
| `_corredor` | ROL_CORREDOR |
| `_operador` | ROL_OPERADOR |
| `_consultor` | ROL_CONSULTOR |
| `_marketing` | ROL_MARKETING |
| `_fertilizante` | ROL_FERTILIZANTE |
| `_chofer` | ROL_CHOFER |
| `_origen`, `_entregador`, `_destinatario` | roles de logística |

## BaseCurl — Configuración por defecto

```php
public $timeout = 60;
public $connectTimeout = 60;
public $verifyHost = false;   // ⚠️ SSL no verificado
public $verifyPeer = false;   // ⚠️ SSL no verificado
public $followLocation = true;
```

## Riesgos

- 🔴 `verifyHost = false` y `verifyPeer = false` — todas las conexiones cURL deshabilitan verificación SSL. Riesgo de man-in-the-middle con sistemas externos.
- 🟡 `User` model en `common\models` — gestión de identidad centralizada en módulo common, no en el core de Yii

## Archivos fuente relevantes

- `source/modules/common/components/BaseCurl.php`
- `source/modules/common/components/rest/ApiRestMuvinController.php`
- `source/modules/common/components/rest/ApiRestController.php`
- `source/modules/common/components/rest/ActiveController.php`
- `source/modules/common/models/`
