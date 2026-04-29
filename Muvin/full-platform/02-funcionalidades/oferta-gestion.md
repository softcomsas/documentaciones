# F-10 — Gestión de Ofertas

## Descripción

Módulo CRUD para que los usuarios administren sus propias **ofertas de compra/venta de granos**: crear, editar, activar/desactivar y eliminar ofertas. También permite gestionar los servicios disponibles dentro de `oferta-app`.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `oferta-app/frontend/src/app/page/ofertas/` |
| Frontend (servicios) | `oferta-app/frontend/src/app/services/` |
| Backend | `oferta-app/backend/` |

## Ruta Angular

`/oferta/ofertas`

## Funcionalidades

- **Listar** ofertas propias con paginación y filtros
- **Crear** nueva oferta (producto, precio, volumen, plazo, condición de entrega)
- **Editar** oferta existente
- **Cambiar estado**: activar / desactivar / cerrar oferta
- **Eliminar** oferta

## Flujo de creación

```
1. Usuario completa formulario de oferta
2. Selecciona: producto, volumen, precio, condición de pago, plazo
3. POST → crear oferta
4. Oferta aparece en el Tablero (F-09) una vez activa
5. Al vencer el plazo o cerrar manualmente → estado CERRADA
```

## Servicios Angular

- `oferta-app/frontend/src/app/services/` — servicios HTTP hacia el backend de ofertas
- `oferta-app/frontend/src/app/shared/` — componentes compartidos del módulo
