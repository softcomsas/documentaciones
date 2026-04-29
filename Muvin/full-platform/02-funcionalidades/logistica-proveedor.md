# F-14 — Cupos Disponibles para Proveedor

## Descripción

Módulo de `logistica-app` que permite a los usuarios con perfil **proveedor** consultar los **cupos de descarga disponibles** en los terminales, con detalle de capacidad, producto y fechas.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend | `logistica-app/frontend/src/app/page/proveedor/cupos-disponible-proveedor/` |
| Backend | `logistica-app/backend/` o microservicio compartido |

## Estructura del módulo

```
cupos-disponible-proveedor/
├── container-cupos-disponibles-proveedor/   ← wrapper con estado
├── cupos-disponibles-proveedor-list/        ← tabla de cupos
├── interfaces/                              ← tipado TypeScript
├── models/                                  ← modelos de dominio
├── services/                                ← servicios HTTP
├── cupos-disponibles-proveedor.module.ts
├── cupos-disponibles-proveedor-routing.module.ts
└── mat-module-cupos-disponibles.module.ts
```

## Ruta Angular

`/logistica/proveedor/cupos-disponibles`

## Funcionalidades

- **Listar cupos disponibles** filtrados por: terminal, producto, fecha
- **Ver detalle** de cada cupo: volumen disponible, condiciones, estado
- Vista **solo lectura** para el proveedor (sin capacidad de modificar)
- Paginación de resultados

## Patrón arquitectónico

Implementa el patrón **Container / Presentational**:
- `container-cupos-disponibles-proveedor` → maneja estado, suscripciones, llamadas a servicios
- `cupos-disponibles-proveedor-list` → componente presentacional, solo recibe `@Input()` y emite `@Output()`

## Relación con otros módulos

- Complementa **descargas-app** (F-01 a F-08): los cupos visibles aquí son los mismos que gestiona el equipo de Descargas, pero expuestos en modo lectura al proveedor.
- Los datos provienen del mismo backend de cupos (ms-legacy o descargas-app backend).
