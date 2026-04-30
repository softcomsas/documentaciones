# Endpoints de Pedidos

> **Archivo fuente:** `src/app/pages/pedidos/services/pedidos.service.ts`
> **Base URL:** `environment.apiURL`

---

## `GET pedido/pedido-centro2`

**Propósito:** Listado paginado de pedidos para el rol centro (rol `3`).
**Autenticación:** Token en `localStorage` (⚠️ verificar si se inyecta via interceptor)
**Consumido por:** [[pedidos-listado]], [[modulo-pedidos]]

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|--------|-----------|------|-------------|-------------|
| page | query | int | sí | Número de página |
| per-page | query | int | sí | Registros por página (fijo: 10) |
| Search[producto_nombre] | query | string | no | Filtro por nombre de producto |
| Search[fecha_desde] | query | date | no | Fecha de inicio del rango |
| Search[fecha_hasta] | query | date | no | Fecha de fin del rango |
| Search[en_tiempo] | query | int | sí (fijo=1) | Solo pedidos en tiempo activo |

### Respuestas

| Código | Significado | Ejemplo |
|--------|-------------|---------|
| 200 | OK | `{data: [...], _meta: {pageCount, totalCount, currentPage}}` |
| 401 | No autenticado | — |

---

## `GET pedido/by-dador`

**Propósito:** Listado de pedidos para el rol dador de carga (rol `5`).
**Autenticación:** Token requerido
**Consumido por:** [[pedidos-listado]]

### Parámetros

Mismo esquema de paginación y filtros que `pedido/pedido-centro2`.

---

## `GET pedido/verify-cliente-proveedor`

**Propósito:** Verificar si un CUIT es cliente-proveedor válido.
**Autenticación:** Token requerido
**Consumido por:** [[modulo-pedidos]] (flujo de alta de pedido)

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|--------|-----------|------|-------------|-------------|
| cuit | query | string | sí | CUIT a verificar |

---

## `GET pedido/verify-cliente-proveedor-and-destino`

**Propósito:** Verificar si un CUIT es válido para un destino dado.
**Autenticación:** Token requerido
**Consumido por:** [[modulo-pedidos]]

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|--------|-----------|------|-------------|-------------|
| cuit | query | string | sí | CUIT a verificar |
| destino | query | string/int | sí | ID o nombre del destino |

---

> [!warning] Endpoints comentados (legacy)
> En `pedidos.service.ts` existen varios métodos comentados (`getAllPedidos_sp`, `getPedido`) que apuntaban a rutas como `pedido` y `pedido/pedido-centro2` con la API de Yii2 anterior. Indican un refactor en curso. No deben reactivarse sin revisión.
