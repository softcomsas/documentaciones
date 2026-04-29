# Endpoints HTTP — PluginController

**Ruta:** `source/controllers/PluginController.php`  
**Base URL:** `/plugin`

## Descripción

`PluginController` expone endpoints HTTP para disparar manualmente los procesos del plugin. En producción, estos endpoints son invocados por el cron vía `yii lector` (no directamente), pero están disponibles para debugging y ejecución manual.

> ⚠️ **Sin autenticación**: Los endpoints de `/plugin/*` no requieren autenticación HTTP. Cualquiera con acceso a la red puede dispararlos.

## Endpoints

### `GET /plugin`
**Método:** `actionIndex()`  
**Descripción:** Ejecuta todos los procesos activos para todos los clientes activos (equivalente a `yii lector`).  
**Lógica:** Llama a `Ejecutor::correrProcesos()`  
**Respuesta:** Array con los resultados de cada proceso.

---

### `GET /plugin/proceso-aca`
**Método:** `actionProcesoAca()`  
**Descripción:** Ejecuta solo el proceso ACA (ID proceso = 1).  
**Lookup:** `Proceso::getOne(['id' => 1, 'activo' => 1])` → `ClienteConfiguracion::getConfiguracionByProcesoId()`

---

### `GET /plugin/procesar-bunge`
**Método:** `actionProcesarBunge()`  
**Descripción:** Ejecuta el proceso Bunge (ID proceso = 3).

---

### `GET /plugin/procesar-samsa`
**Método:** `actionProcesarSamsa()`  
**Descripción:** Ejecuta el proceso SAMSA (ID proceso = 4).

---

### `GET /plugin/procesar-cofco`
**Método:** `actionProcesarCofco()`  
**Descripción:** Ejecuta el proceso Cofco (ID proceso = 5).

---

### `GET /plugin/procesar-molinos-agro`
**Método:** `actionProcesarMolinosAgro()`  
**Descripción:** Ejecuta el proceso MolinosAgro (ID proceso = 6).

---

### `GET /plugin/procesar-dreyfus`
**Método:** `actionProcesarDreyfus()`  
**Descripción:** Ejecuta el proceso Dreyfus (ID proceso = 2).

---

### `GET /plugin/procesar-cofco-tomas-hnos`
**Método:** `actionProcesarCofcoTomasHnos()`  
**Descripción:** Ejecuta Cofco para el cliente Tomas Hnos.  
**⚠️ ID hardcodeado:** `$id_cliente_configuracion = 20` — no usa lookup por proceso.

---

### `GET /plugin/proceso-tomas-hnos-dreyfus`
**Método:** `actionProcesoTomasHnosDreyfus()`  
**Descripción:** Ejecuta Dreyfus para el cliente Tomas Hnos/Dreyfus.  
**⚠️ ID hardcodeado:** `$id_cliente_configuracion = 24`

---

### `GET /plugin/proceso-fyo`
**Método:** `actionProcesoFyo()`  
**Descripción:** Ejecuta el proceso Fyo para Tomas Hnos.  
**⚠️ ID hardcodeado:** `$id_cliente_configuracion = 26`

---

### `GET /plugin/cargar-productos`
**Método:** `actionCargarProductos()`  
**Descripción:** Sincroniza la tabla `productos` desde Muvin API.  
**Lógica:** `Productos::CargarProductos()`

---

### `GET /plugin/cargar-destinos`
**Método:** `actionCargarDestinos()`  
**Descripción:** Sincroniza la tabla `destinos` desde Muvin API.  
**Lógica:** `Destinos::CargarDestinos()`

---

### `GET /plugin/cargar-onccas-productos`
**Método:** `actionCargarOnccasProductos()`  
**Descripción:** Sincroniza la tabla `oncca_destino_producto` desde Muvin API.

---

## Manejo de errores

Todos los endpoints envuelven su lógica en `try/catch`:
- En caso de excepción, retornan HTTP 404 con el mensaje del error
- Si el proceso no existe o está inactivo, el mensaje es `"No se encontró el proceso"`

## Swagger

Los endpoints están documentados con anotaciones `@SWG` (Swagger 2.0). La documentación generada está disponible en `/swagger` (via `SwaggerController`).

## Resumen de IDs hardcodeados

| Endpoint | ID hardcodeado | Riesgo |
|---|---|---|
| `/procesar-cofco-tomas-hnos` | `cliente_configuracion.id = 20` | Si cambia el registro en BD, el endpoint falla silenciosamente |
| `/proceso-tomas-hnos-dreyfus` | `cliente_configuracion.id = 24` | Ídem |
| `/proceso-fyo` | `cliente_configuracion.id = 26` | Ídem |
