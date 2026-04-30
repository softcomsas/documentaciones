# Inventario de Reportes y Asistentes — App Agronomy

> **Última revisión:** 2026-04-30

## Reportes / Vistas de consulta

| Nombre | Tipo | Propósito | Módulo | Componente | Datos consumidos | Estado |
|--------|------|-----------|--------|-----------|-----------------|--------|
| Listado de pedidos | 📊 Reporte/Listado | Tabla paginada de pedidos del centro | [[modulo-pedidos]] | `PedidosComponent` | `pedido/pedido-centro2`, `pedido/by-dador` | Activo |
| Ver cupos de reserva | 📊 Reporte | Cupos disponibles por reserva/origen | [[modulo-pedidos]] | `VerCuposReservaComponent` | `seguimiento/filtrar-reservas` | Activo |
| Detalle de cupos | 📊 Reporte | Detalle granular de cupos | [[modulo-pedidos]] | `DetalleCuposComponent` | `seguimiento/detalle-reservas` | Activo |
| Detalle reserva - productos | 📊 Reporte | Productos involucrados en una reserva | [[modulo-pedidos]] | `DetalleReservaProductosComponent` | `seguimiento/detalle-reservas-fertilizante` | Activo |
| Capacidad de terminal | 📊 Reporte | Disponibilidad en terminal por fecha | [[modulo-pedidos]] | (embebido en filtros) | `seguimiento/filtrar-capacidad-terminal` | Activo |
| Listado de transportistas | 📊 Reporte/Listado | Tabla de transportistas vinculados | [[modulo-transportistas]] | `TransportistasComponent` | `camion` | Activo |

## Asistentes (Wizards)

| Nombre | Tipo | Propósito | Módulo | Pasos | Estado |
|--------|------|-----------|--------|-------|--------|
| Agregar pedido | 🧙 Wizard | Flujo multi-paso para crear pedido/reserva | [[modulo-pedidos]] | 1. Selección origen/producto → 2. Destino → 3. Tipo despacho → 4. Productos (mezcla) → 5. Confirmación | Activo |

> [!info] Exportaciones
> Se detectó `ExcelService` en `shared/services/exel.service.ts` (⚠️ typo en nombre del archivo). Sugiere que existe funcionalidad de exportación a Excel. 🚧 Pendiente identificar qué listados usan esta exportación.
