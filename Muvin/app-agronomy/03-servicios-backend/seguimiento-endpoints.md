# Endpoints de Seguimiento y Reservas

> **Archivo fuente:** `src/app/pages/pedidos/services/reservas.service.ts`
> **Base URL:** `environment.apiURL`

---

## `GET seguimiento/filtrar-reservas`

**Propósito:** Obtener el listado de reservas con filtros opcionales.
**Autenticación:** Token requerido
**Consumido por:** [[modulo-pedidos]]

### Parámetros (opcionales vía query string)

| Nombre | Tipo | Descripción |
|--------|------|-------------|
| id_origen | int | Filtro por origen/terminal |
| fecha | date | Fecha de la reserva |
| id_grupo_cliente | int | Grupo de cliente |
| id_cuenta_cliente | int | Cuenta del cliente |
| id_zona | int | Zona geográfica |
| page | int | Página |
| per-page | int | Registros por página |

---

## `GET seguimiento/filtrar-capacidad-terminal`

**Propósito:** Obtener la capacidad disponible en una terminal para una fecha.
**Consumido por:** [[modulo-pedidos]]

### Parámetros

Mismos filtros que `filtrar-reservas` (sin paginación).

### Respuesta

```json
{ "data": { "capacidad_terminal": [...] } }
```

---

## `GET seguimiento/detalle-reservas`

**Propósito:** Detalle de reservas de granos para un cliente/origen/fecha.
**Consumido por:** [[pedidos-detalle-cupos]], [[modulo-pedidos]]

### Parámetros

| Nombre | Tipo | Obligatorio |
|--------|------|-------------|
| id_cuenta_cliente | int | sí |
| id_origen | int | sí |
| fecha | date | sí |
| estado | string | sí |
| id_pedido | int | no |

---

## `GET seguimiento/detalle-reservas-fertilizante`

**Propósito:** Detalle de reservas de fertilizantes.
**Consumido por:** [[modulo-pedidos]]

Mismos parámetros que `detalle-reservas`.

---

## `GET seguimiento/ver-cupo-proveedor`

**Propósito:** Ver cupo del proveedor para una reserva.

### Parámetros

| Nombre | Tipo | Obligatorio |
|--------|------|-------------|
| id_reserva | int | sí |

---

## `GET seguimiento/ver-cupo-cliente`

**Propósito:** Ver cupo del cliente para una reserva.

Mismo esquema que `ver-cupo-proveedor`.

---

## `GET seguimiento/select-zona-cliente`

**Propósito:** Obtener select/lista de zonas de clientes.

---

## `GET seguimiento/select-grupo-cliente`

**Propósito:** Obtener select/lista de grupos de clientes.

---

## `GET seguimiento/select-clientes`

**Propósito:** Obtener select/lista de clientes.

---

## `GET seguimiento/cuit-verify`

**Propósito:** Verificar/buscar datos por CUIT o razón social.

### Parámetros

| Nombre | Ubicación | Descripción |
|--------|-----------|-------------|
| razon_social | query | CUIT o razón social a buscar |

---

## `GET seguimiento/comercial-detalle-reservas-cliente`

**Propósito:** Detalle de reservas para vista de centro (rol `3`).

### Parámetros

| Nombre | Tipo | Descripción |
|--------|------|-------------|
| id_cuenta_cliente | int | — |
| id_origen | int | — |
| fecha | date | — |
| id_reserva | int | — |
| estado | string | — |
| id_pedido | int | opcional |

---

## `GET seguimiento/comercial-seguimiento-detalle-reservas`

**Propósito:** Detalle de seguimiento de reservas para otros roles.

Mismos parámetros que el endpoint anterior.
