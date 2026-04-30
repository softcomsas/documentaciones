# Índice de Servicios Backend — app-clients

> [[README]] · [[_indice-modulos]] · [[_indice-funcionalidades]]

## Base URL

```
https://panel.muvinapp.com/api/backend/web/
```

Headers requeridos en todos los endpoints:
```
X-Api-Key: uH9hatw8G3lQls7fE3
Authorization: Bearer {access_token}
```

## Grupos de endpoints

| Grupo | Archivo | Descripción |
|-------|---------|-------------|
| [Auth](./auth-endpoints.md) | `auth-endpoints.md` | Login, refresh |
| [Cupos](./cupos-endpoints.md) | `cupos-endpoints.md` | CRUD cupos, solicitudes, demandas |
| [Cargas](./cargas-endpoints.md) | `cargas-endpoints.md` | Pedidos, cargas, pedido rápido |
| [Catálogos](./catalogos-endpoints.md) | `catalogos-endpoints.md` | Cabeceras, CCPP, alfanuméricos |

## Resumen de todos los endpoints

| Verbo | Ruta | Módulo | Función |
|-------|------|--------|---------|
| POST | `login` | Auth | Autenticación |
| GET | `v3/cupo/disponibles` | Cupos | Cupos para CF |
| GET | `v3/cupo/listado` | Cupos | Cupos para Admin |
| GET | `v2/cupos/buscar-x-demandante` | Cupos | Búsqueda por demandante |
| POST | `v3/cupo/asignar` | Cupos | Asignar cupo |
| POST | `v3/cupo/asignar2` | Cupos | Asignar cupo (variante) |
| POST | `v3/cupo/recuperar` | Cupos | Recuperar cupo |
| POST | `v3/cupo/carga-solicitud` | Cupos | Solicitud simple |
| POST | `v3/cupo/carga-solicitud-distribuida` | Cupos | Solicitud distribuida |
| GET | `v2/cupos/demandados/:fecha` | Cupos | Mis solicitudes |
| GET | `demanda-cupo/by-demandante` | Cupos | Demandas por rango |
| POST | `demanda-cupo/variar-cantidad` | Cupos | Modificar cantidad |
| PUT | `v3/cupo/:id` | Cupos/Cargas | Asociar entregador |
| GET | `v2/cupos/disponibles` | Cargas | Cupos para cargar |
| POST | `pedido` | Cargas | Crear pedido |
| POST | `pedido/rapido` | Cargas | Pedido rápido |
| GET | `solicitud/:id` | Cargas | Detalle solicitud |
| GET | `v3/cabecera/select` | Catálogos | Listado cabeceras |
| GET | `v3/ccpp/:id` | Catálogos | Datos CCPP |
| GET | `alfanumerico` | Catálogos | Valores alfanuméricos |
