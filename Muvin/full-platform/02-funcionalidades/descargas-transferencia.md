# F-05 — Transferencia de Cupo

## Descripción

Permite **transferir un cupo** de descarga de un titular a otro. El proceso genera un registro de transferencia (RT — Registro de Transferencia) que debe ser aprobado por un verificador antes de hacerse efectivo.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `descargas-app/frontend/src/app/page/transferencia/` |
| Frontend (componente) | `descargas-app/frontend/src/app/components/transferencia/` |
| Backend (controller) | `descargas-app/backend/src/controllers/transferencia.controller.ts` |
| Backend (servicio) | `descargas-app/backend/src/services/transferencia.service.ts` |

## Ruta Angular

`/descargas/transferencia`

## Endpoints backend

| Método | Ruta | Descripción |
|---|---|---|
| `POST` | `/descargas/rt` | Crear transferencia (devuelve COE) |
| `PUT` | `/descargas/rt` | Actualizar estado de transferencia |
| `GET` | `/descargas/rt` | Listar transferencias con filtros paginados |
| `GET` | `/reportes/rt` | Exportar transferencias a Excel |

## Rol requerido

`DES Verificador RT`

## DTOs relevantes

| DTO | Uso |
|---|---|
| `InsertTransferenciaDTO` | Crear nueva transferencia |
| `UpdateTransferenciaDTO` | Actualizar estado de transferencia |
| `FilterTransferenciasDto` | Filtros para listar transferencias |

## Flujo

```
1. Usuario selecciona cupo a transferir
2. Completa datos del destinatario
3. POST /descargas/rt → crea transferencia, retorna COE
4. Verificador RT aprueba o rechaza
5. PUT /descargas/rt → actualiza estado
```

## Resultado

Al crear una transferencia exitosa, el backend retorna el **COE** (Código de Operación Electrónica) generado por AFIP que identifica la transferencia.
