# Endpoints: Centros y Vinculaciones

> **Servicio principal:** `CentrosService` (`shared/services/centros.service.ts`)
> **Líneas:** ~800+
> **API:** `GlobalService.apiHost`
> **Endpoints:** ~70
> **Consumido por:** [[modulo-admin]], [[modulo-cupera]], [[modulo-cupo]], [[modulo-destino]]

---

## Propósito

Servicio más grande del sistema. Concentra CRUD de centros, todas las vinculaciones entre actores (centro-cliente, centro-corredor, centro-transporte, etc.), gestión de lista negra, estadísticas, viajes, pedidos, búsqueda de flota, reportes por centro, notificaciones push (OneSignal) y configuración de centro.

---

## Endpoints — CRUD Centro

| Verbo | Ruta | Propósito | Parámetros |
|---|---|---|---|
| GET | `centro` | Listar centros (paginado) | `page`, `per_page`, `Search[razon_social]`, `Search[cuit_cuil]` |
| GET | `centro/select` | Dropdown de centros | — |
| GET | `centro/{id}` | Detalle de centro | `id` (path) |
| POST | `centro` | Crear centro | Body: datos del centro |
| PUT | `centro/{id}` | Actualizar centro | `id` (path), Body |
| DELETE | `centro/{id}` | Eliminar centro | `id` (path) |

---

## Endpoints — Vinculaciones (13 tipos)

Patrón repetido: `GET` (paginado) + `POST` (crear) + `DELETE` (eliminar) para cada tipo de relación.

| Tipo | GET (listar) | POST (crear) | DELETE (eliminar) |
|---|---|---|---|
| Centro-Cliente | `centro-cliente` | `centro-cliente` | `centro-cliente/{id}` |
| Centro-Corredor | `centro-corredor` | `centro-corredor` | `centro-corredor/{id}` |
| Centro-Transporte | `centro-transporte` | `centro-transporte` | `centro-transporte/{id}` |
| Centro-Operador | `centro-operador` | `centro-operador` | `centro-operador/{id}` |
| Centro-Entregador | `centro-entregador` | `centro-entregador` | `centro-entregador/{id}` |
| Centro-Intermediario | `centro-intermediario` | `centro-intermediario` | `centro-intermediario/{id}` |
| Centro-Destinatario | `centro-destinatario` | `centro-destinatario` | `centro-destinatario/{id}` |
| Centro-Empresa | `centro-empresa` | `centro-empresa` | `centro-empresa/{id}` |

> Cada GET acepta `page`, `per_page`, `Search[razon_social]`, `Search[cuit_cuil]`.

---

## Endpoints — Pedidos y Viajes

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `pedido/by-centro` | Pedidos por centro (paginado) |
| GET | `viaje/lista-historico` | Historial de viajes |
| GET | `viaje/en-curso` | Viajes en curso |
| GET | `viaje/lista` | Listado de viajes (paginado) |
| POST | `pedido` | Crear pedido |
| PUT | `pedido/{id}` | Actualizar pedido |

---

## Endpoints — Lista negra

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `lista-negra` | Listar lista negra (paginado) |
| POST | `lista-negra` | Agregar a lista negra |
| DELETE | `lista-negra/{id}` | Quitar de lista negra |

---

## Endpoints — Estadísticas y Reportes

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `centro/estadistica` | Estadísticas del centro |
| GET | `centro/ranking` | Rankings por centro |
| GET | `centro/reporte-turneadas` | Reporte de turneadas |
| GET | `centro/reportes` | Reportes generales |

---

## Endpoints — Búsqueda y Flota

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `busqueda` | Búsqueda general |
| GET | `busqueda/{id}` | Detalle de búsqueda |
| POST | `busqueda` | Crear búsqueda de flota |
| GET | `flota-intermediario` | Flota de intermediario |

---

## Endpoints — Configuración y Notificaciones

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `centro/configuracion` | Configuración del centro |
| PUT | `centro/configuracion` | Actualizar configuración |
| POST | `onesignal/push` | Push notification (OneSignal) |
| POST | `centro/logo` | Subir logo del centro |

---

## Patrones notables

- **Paginación**: `page` + `per_page` en todos los listados
- **Búsqueda**: patrón `Search[campo]=valor` en query params
- **OneSignal**: integración push directa desde servicio frontend

---

## Auditorías — `AuditoriasService`

> **Archivo:** `auditorias.service.ts`

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `auditoria` | Listado de auditoría (paginado) |
| GET | `auditoria/{id}` | Detalle de auditoría |
| GET | `auditoria/interna` | Auditoría interna |

## Auditoría Centro — `AuditoriaCentroService`

> **Archivo:** `auditoria-centro.service.ts`

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `auditoria-interna-centro` | Auditoría interna del centro |
| GET | `auditoria-interna-centro/detalles` | Detalles de auditoría |

## Error Log — `ErrorLogCentroService`

> **Archivo:** `errorlog-centro.service.ts`

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `errorlog-centro` | Logs de error del centro |

## Reportes — `ReportesService`

> **Archivo:** `reportes.service.ts`

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `reportes/centro` | Reportes por centro |
| GET | `reportes/turneada` | Reporte de turneada |
| GET | `reportes/viajes` | Reporte de viajes |
| GET | `reportes/ranking` | Ranking general |

---

## Archivos fuente

- `src/app/shared/services/centros.service.ts` — Servicio principal (~800 líneas)
- `src/app/shared/services/auditorias.service.ts`
- `src/app/shared/services/auditoria-centro.service.ts`
- `src/app/shared/services/errorlog-centro.service.ts`
- `src/app/shared/services/reportes.service.ts`

---

## Referencias

- [[_indice-servicios]] — Índice general
- [[modulo-admin]] — Principal consumidor
- [[personas-endpoints]] — Personas referenciadas en vinculaciones
