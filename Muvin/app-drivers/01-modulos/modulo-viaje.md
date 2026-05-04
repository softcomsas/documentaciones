# Módulo: Viaje

> **Ruta:** `lib/src/pages/viaje_page.dart`
> **Criticidad:** 🔴 Alta
> **Estado:** Activo

## Propósito

Muestra el detalle completo del viaje asignado al chofer. Es el destino de la acción "Ver mi viaje" desde `HomePage` cuando el campo `pdf` está disponible.

## Funcionalidades

| # | Funcionalidad | Descripción |
|---|---------------|-------------|
| 1 | Ver viaje actual | Muestra los datos del pedido asignado al chofer |
| 2 | Ver carta de porte | Botón que navega a `/cartaporte` (página **ROTA**) |
| 3 | Datos de contacto | Muestra info del cliente/destino |

## Origen de datos

Los datos provienen de `obtenerMiViaje()` → `GET chofer-app/mi-viaje`.

## Dependencias

- **Depende de:** [[modulo-muvin-provider]], [[modulo-home]]
- **Enlaza a:** [[modulo-carta-porte]] ← ROTO
- **Endpoints:** `GET chofer-app/mi-viaje`

## Riesgos

- 🔴 Navega a `/cartaporte` que crashea al construirse (ver [[modulo-carta-porte]]).
