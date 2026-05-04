# Módulo: Cargas

> **Rutas:** `lib/src/pages/cargas_page.dart`, `lib/src/pages/cargas_detalle_page.dart`
> **Criticidad:** 🔴 Alta
> **Estado:** Activo

## Propósito

Muestra los pedidos de transporte públicos disponibles en un carrusel de tarjetas (`flutter_swiper`). El chofer puede ver el detalle de cada carga y postularse o cancelar su postulación.

## Funcionalidades

| # | Funcionalidad | Descripción |
|---|---------------|-------------|
| 1 | Listar cargas | Llama `obtenerCargas()` → `GET chofer-app/pedidos-publicos` |
| 2 | Carrusel de tarjetas | Swiper con cada carga como card |
| 3 | Mostrar detalle | Navega a `/cargasdetalle` con los datos de la carga seleccionada |
| 4 | Postularse | Desde detalle → `POST chofer-app/postularme?id_pedido=X` |
| 5 | Cancelar postulación | Desde detalle → `POST chofer-app/des-postularme?id_pedido=X` |

## Datos mostrados por carga

Los campos vienen del array `data` de la respuesta del backend. Se normaliza `nombreDestino = 'XXXXXXXXXX'` si viene vacío.

## Dependencias

- **Depende de:** [[modulo-muvin-provider]]
- **Endpoints:** `GET chofer-app/pedidos-publicos`, `POST chofer-app/postularme`, `POST chofer-app/des-postularme`

## Riesgos

- ⚠️ `flutter_swiper ^1.1.6` está abandonado — no tiene soporte para Flutter 2+/null safety.
- ⚠️ Si no hay cargas, muestra texto pero no hay opción de pull-to-refresh.
- ⚠️ El `FutureBuilder` rebuild en cada frame puede hacer múltiples llamadas HTTP.
