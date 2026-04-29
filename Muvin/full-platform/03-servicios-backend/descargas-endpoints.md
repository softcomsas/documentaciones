# Endpoints — descargas-app (NestJS)

> **Base URL:** `http://<host>:<port>`
> **Autenticación:** JWT Bearer token validado en `TokenMiddleware` (aplica a `*`)
> **Última revisión:** 2026-04-29
> **Consumido por:** [[modulo-descargas]]

---

## `POST /descargas/actualizar-cupo-afip`

**Propósito:** Actualiza los cupos de descarga consultando la API de AFIP con los IDs de cupos indicados.
**Autenticación:** JWT Bearer (TokenMiddleware global)
**Consumido por:** [[modulo-descargas]], [[descargas-gestion-cupos]]

### Payload

```json
{ "cupos": [123, 456, 789] }
```

### Respuestas

| Código | Significado | Ejemplo |
|---|---|---|
| 200 | Actualización exitosa | `{ "success": true, "data": [...diferencias] }` |
| 400 | AFIP no disponible o error de validación | `{ "message": "No se pudo realizar la actualización. El sistema de AFIP no está disponible..." }` |

### Efectos secundarios
- Consulta la API de AFIP externamente.
- Puede actualizar registros de cupos en la base de datos con las diferencias detectadas.

---

## `POST /descargas`

**Propósito:** Crear o actualizar cupos de descarga.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[descargas-gestion-cupos]]

### Payload

```json
{ "cupos": [ { "...campos del cupo..." } ] }
```
> Ver modelo completo en `descargas-app/backend/src/models/cupo.model.ts` (`CuposPayload`)

### Respuestas

| Código | Significado | Ejemplo |
|---|---|---|
| 201 | Cupos guardados exitosamente | `{ "success": true, "status": 200, "data": {...} }` |
| 400 | Error de validación | `{ "success": false, "status": 400, "data": "..." }` |
| 422 | Error de negocio | `{ "success": false, "status": 422, "data": "..." }` |

### Efectos secundarios
- Escribe en tabla de cupos (DB vía Sequelize).
- Puede disparar validaciones contra AFIP internamente.

---

## `PUT /descargas`

**Propósito:** Actualizar el estado de un cupo de descarga existente.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]]

### Payload

```json
{ "...campos de UpdateEstadoPayload..." }
```
> Ver `descargas-app/backend/src/models/cupo.model.ts` (`UpdateEstadoPayload`)

### Respuestas

| Código | Significado |
|---|---|
| 200 | Cupo actualizado exitosamente |
| 400 | Error de validación |
| 404 | Cupo no encontrado |
| 500 | Error interno |

---

## `GET /descargas`

**Propósito:** Consultar cupos de descarga con filtros.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]]

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|---|---|---|---|---|
| ⚠️ Pendiente | query | — | — | Ver `CupoParaGet` en `cupo-para-get.model.ts` |

### Respuestas

| Código | Significado |
|---|---|
| 200 | Lista de cupos |
| 400 | Error de parámetros |

---

## `POST /reportes`

**Propósito:** Generar y descargar reportes en formato Excel.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[descargas-reportes]]

### Payload

```json
{ "⚠️ Pendiente de verificar estructura": "ver reportes.controller.ts" }
```

### Respuestas

| Código | Significado |
|---|---|
| 200/201 | Archivo Excel (buffer o stream) |
| 400/422 | Error de generación |

### Efectos secundarios
- Genera archivo Excel en memoria con ExcelJS.
- Lee datos de cupos/descargas de la DB.

---

## `POST /adenda`

**Propósito:** Crear una adenda asociada a una descarga.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[descargas-adendas]]

### Payload

> ⚠️ Pendiente de verificar — ver `adenda.controller.ts` y `adenda.model.ts`

---

## `POST /transferencia`

**Propósito:** Crear una transferencia de cupo entre terminales.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[descargas-transferencia]]

### Payload

> ⚠️ Pendiente de verificar — ver `transferencia.controller.ts` y `transferencia.model.ts`

---

## `POST /tren`

**Propósito:** Gestionar carta porte para transporte en tren.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[descargas-carta-porte-tren]]

### Payload

> ⚠️ Pendiente de verificar — ver `tren.controller.ts` y `cartaPorteTren.model.ts`

---

## `POST /txt-upload`

**Propósito:** Importación masiva de datos desde archivos TXT.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[descargas-txt-upload]]

---

## `GET /consultar-roles`

**Propósito:** Consultar los roles del usuario autenticado.
**Autenticación:** JWT Bearer
**Consumido por:** [[modulo-descargas]], [[modulo-main-shell]]

> ⚠️ Pendiente de verificar si retorna roles propios del backend o los delega a un servicio externo.

---

## `POST /cron` / `GET /cron`

**Propósito:** Control manual de cron jobs programados.
**Autenticación:** ⚠️ PENDIENTE DE VERIFICAR — posible endpoint sin auth adecuada.
**Consumido por:** Uso interno / administración

> [!danger] Riesgo de seguridad
> El `CronController` expone rutas HTTP para disparar cron jobs. Si no están protegidas correctamente, cualquier cliente podría triggerearlos. Ver [[security-inventory]].
