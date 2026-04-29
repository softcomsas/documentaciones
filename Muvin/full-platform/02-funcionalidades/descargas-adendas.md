# F-04 — Adendas

## Descripción

Permite generar y gestionar **adendas** sobre cartas de porte existentes. Una adenda es una modificación o complemento a una carta porte ya emitida (ej: cambio de destino, actualización de datos de calidad).

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `descargas-app/frontend/src/app/page/adenda/` |
| Frontend (form) | `descargas-app/frontend/src/app/components/adenda-form-card/` |
| Backend (controller) | `descargas-app/backend/src/controllers/adenda.controller.ts` |
| Backend (servicio) | `descargas-app/backend/src/services/adenda.service.ts` |

## Ruta Angular

`/descargas/adenda`

## Endpoints backend

| Método | Ruta | Descripción |
|---|---|---|
| `POST` | `/descargas/ad` | Crear o actualizar adenda |
| `PUT` | `/descargas/ad` | Actualizar estado de adenda |
| `GET` | `/descargas/ad` | Listar adendas con filtros paginados |
| `GET` | `/reportes/adendas` | Exportar adendas a Excel |

## Rol requerido

`DES Verificador AD`

## Lógica de crear vs actualizar

El endpoint `POST /descargas/ad` detecta automáticamente si es inserción o actualización:

```typescript
const esActualizacion = 'id' in dto && (dto.id !== null && String(dto.id) != "");
// Si tiene id → updateAdenda()
// Si no tiene id → crearAdenda()
```

## DTOs relevantes

| DTO | Uso |
|---|---|
| `InsertAdendaDTO` | Crear nueva adenda |
| `UpdateAdendaDTO` | Actualizar datos de adenda existente |
| `UpdateEstadoAdendaDTO` | Cambiar estado de adenda (PUT) |
| `FilterAdendaDto` | Filtros para listar adendas |

## Flujo

```
1. Usuario selecciona una carta porte existente
2. Completa el formulario de adenda (AdendaFormCardComponent)
3. POST /descargas/ad → crear adenda
4. Sistema envía a AFIP/SCE para validación
5. PUT /descargas/ad → actualizar estado según respuesta
```
