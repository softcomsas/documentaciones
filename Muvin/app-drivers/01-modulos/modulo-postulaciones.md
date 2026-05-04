# Módulo: Postulaciones

> **Ruta:** `lib/src/pages/postulaciones_page.dart`
> **Criticidad:** 🔴 Alta
> **Estado:** Activo

## Propósito

Muestra las postulaciones activas del chofer (cargas a las que ya se postuló). Permite cancelar una postulación existente.

## Funcionalidades

| # | Funcionalidad | Descripción |
|---|---------------|-------------|
| 1 | Listar postulaciones | Llama `obtenerPostulaciones()` → `GET chofer-app/postulaciones` |
| 2 | Ver detalle | Muestra datos del pedido con el estado de la postulación |
| 3 | Cancelar postulación | `POST chofer-app/des-postularme?id_pedido=X` |

## Estructura de la respuesta

El endpoint retorna array de objetos pedido con el campo `postulado: true/false`.

## Dependencias

- **Depende de:** [[modulo-muvin-provider]]
- **Endpoints:** `GET chofer-app/postulaciones`, `POST chofer-app/des-postularme`

## Riesgos

- ⚠️ Estructura similar a [[modulo-cargas]] — código duplicado entre ambas páginas.
- ⚠️ No hay manejo de error visual si la cancelación falla.
