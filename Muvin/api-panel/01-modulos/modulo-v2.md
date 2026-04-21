# Módulo V2 — API de Cupos Legacy

> **Última revisión:** 2026-04-21
> **Namespace:** `v2\`
> **Ruta:** `backend/modules/v2/`
> **Ver también:** [[modulo-v3]], [[modulo-cupos]], [[deuda-tecnica]]

---

## Propósito

El módulo **v2** es la versión anterior de la API de cupos. Está en modo **legacy** — se mantiene por compatibilidad con clientes que aún no migraron a v3, pero no recibe nuevas funcionalidades.

---

## Estado actual

| Estado | Descripción |
|--------|-------------|
| 🟡 Legacy | Funcional, sin nuevas features |
| 🚧 Migración pendiente | Los clientes en v2 deben migrar a v3 |
| ⚠️ Sin carta de porte | No incluye integración CCPP/AFIP |

---

## Controladores

| Controlador | Propósito |
|-------------|-----------|
| `DefaultController.php` | Stub de módulo (actionIndex) |
| `CupoController.php` | Gestión completa de cupos v2 |

---

## Endpoints de CupoController

| Acción | Ruta REST | Propósito |
|--------|-----------|-----------|
| `actions()` | — | Mapa de verbos REST (GET/POST/PUT) |
| `actionObtenerCartaPorte` | `GET v2/cupos/obtener-carta-porte?idCupo=X` | Obtener carta de porte del cupo |
| `actionDadorTurno` | `GET v2/cupos/dador-turno` | Cupos del dador con turno |
| `actionDisponibles` | `GET v2/cupos/disponibles?fecha=X` | Cupos disponibles para una fecha |
| `actionDemandados` | `GET v2/cupos/demandados?fecha=X` | Cupos demandados para una fecha |
| `actionAsignar` | `POST v2/cupos/asignar` | Asignar cupo a un viaje/chofer |
| `actionBuscarXDemandante` | `GET v2/cupos/buscar-x-demandante` | Buscar cupos por empresa demandante |
| `actionRecuperar` | `POST v2/cupos/recuperar` | Recuperar cupo devuelto |
| `actionDashboard` | `GET v2/cupos/dashboard?fecha=X` | Dashboard de cupos por fecha |

---

## Diferencias con V3

| Funcionalidad | V2 | V3 |
|--------------|----|----|
| Carta de Porte Electrónica | ❌ | ✅ |
| Integración AFIP CTG | ❌ | ✅ |
| Zonas geográficas avanzadas | ❌ | ✅ |
| Contratos | ❌ | ✅ |
| Cabeceras de cupo | ❌ | ✅ |
| Auditoría de cambios | Básica | ✅ Avanzada |

---

## Plan de deprecación

> [!warning] Deuda técnica
> Este módulo debe deprecarse una vez que todos los clientes migren a v3. Ver [[deuda-tecnica]] y [[recomendaciones-modernizacion]] para el plan de migración.
