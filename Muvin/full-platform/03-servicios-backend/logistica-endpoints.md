# Endpoints — logistica-app (Yii2 PHP)

> **Base URL:** `http://<host>:<port>/api/`
> **Autenticación:** ⚠️ Pendiente de verificar mecanismo exacto (posiblemente token header o sesión Yii2)
> **Última revisión:** 2026-04-29
> **Consumido por:** [[modulo-logistica]]

> [!info] Nota
> Los endpoints de los backends Yii2 siguen el patrón REST de Yii2 (`yii\rest\ActiveController`). Las rutas se infieren de los nombres de controladores. Requieren verificación en `config/web.php` para las reglas de URL exactas.

---

## Recursos REST (inferidos de controladores)

| Controlador | Ruta estimada | Métodos | Entidad |
|---|---|---|---|
| `ViajeController` | `/viaje` | GET, POST, PUT, DELETE | [[entidad-viaje]] |
| `CargaController` | `/carga` | GET, POST, PUT, DELETE | [[entidad-carga]] |
| `CargaDerivadaController` | `/carga-derivada` | GET, POST, PUT, DELETE | Carga derivada |
| `CargaDetalleController` | `/carga-detalle` | GET, POST, PUT, DELETE | Detalle de carga |
| `ChoferController` | `/chofer` | GET, POST, PUT, DELETE | [[entidad-chofer]] |
| `DerivacionController` | `/derivacion` | GET, POST, PUT, DELETE | [[entidad-derivacion]] |
| `DestinoController` | `/destino` | GET, POST, PUT, DELETE | Destino |
| `EquipoController` | `/equipo` | GET, POST, PUT, DELETE | [[entidad-equipo]] |
| `EstadoViajeController` | `/estado-viaje` | GET, POST, PUT, DELETE | Estado de viaje |
| `IntermediarioController` | `/intermediario` | GET, POST, PUT, DELETE | Intermediario |
| `MotivoCancelacionController` | `/motivo-cancelacion` | GET | Catálogo motivos cancelación |
| `OfertaController` | `/oferta` | GET, POST, PUT, DELETE | ⚠️ Ver nota de solapamiento con [[modulo-oferta]] |
| `OrigenController` | `/origen` | GET, POST, PUT, DELETE | Origen |
| `ProductoController` | `/producto` | GET | Catálogo de productos/granos |
| `SiteController` | `/site/login`, `/site/logout` | POST | Autenticación Yii2 |

## Notas

- ⚠️ Los parámetros exactos, payloads y respuestas requieren revisión del código PHP de cada controlador.
- ⚠️ `OfertaController` en logistica puede ser un duplicado o una versión diferente del de `oferta-app`.
- La ruta base puede ser `/api/v1/` o `/api/` según la config de URL rules en `config/web.php`.
