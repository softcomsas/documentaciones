# Módulo Agroquímicos

> **Última revisión:** 2026-04-21
> **Namespace:** `agroquimicos\`
> **Ruta:** `backend/modules/agroquimicos/`
> **Ver también:** [[modulo-fertilizantes]], [[_indice-modulos]]

---

## Propósito

El módulo **agroquimicos** gestiona los **pedidos de agroquímicos** (pesticidas, herbicidas, fungicidas), operando de forma análoga al módulo de fertilizantes pero para productos fitosanitarios.

---

## Controladores

| Controlador | Propósito |
|-------------|-----------|
| `PedidoController.php` | Gestión de pedidos de agroquímicos |

---

## Endpoints

| Acción | Propósito |
|--------|-----------|
| `actions()` | Mapa de verbos REST (GET/POST/PUT/DELETE) |
| `actionView($numeroPedido)` | Obtener detalle de pedido por número |

---

## Notas

> [!note] Módulo simple
> Es uno de los módulos más sencillos del sistema. Un único controlador con CRUD estándar. Poca o nula integración con servicios externos conocidos.
