# Comandos de Consola

**Ruta:** `source/commands/`

Los comandos de consola son la interfaz principal de operación del plugin. Se ejecutan via `php yii <comando>` y son invocados por el cron y por operadores.

## Comandos disponibles

### `yii lector` — LectorController

**Archivo:** `commands/LectorController.php`  
**Propósito:** Ejecuta el ciclo completo de descarga y procesamiento de cupos.

```bash
php yii lector
```

**Lógica interna:**
```php
class LectorController extends Controller {
    public function actionIndex() {
        Ejecutor::correrProcesos();
    }
}
```

**Efectos:**
1. Conecta a los servidores IMAP de todos los clientes activos
2. Descarga emails no leídos
3. Parsea adjuntos (PDF/HTML/Excel)
4. Guarda `CupoDocumento` con estado `PENDIENTE`
5. Llama a la Muvin API para cargar cada cupo
6. Actualiza el estado a `PROCESADO` o `ERROR`
7. Marca los emails como leídos

---

### `yii parametros` — ParametrosController

**Archivo:** `commands/ParametrosController.php`  
**Propósito:** Sincroniza los catálogos de referencia desde la Muvin API hacia la base de datos local.

```bash
php yii parametros
```

**Lógica interna:** Llama en secuencia a:
1. `Productos::CargarProductos()` → tabla `productos`
2. `Destinos::CargarDestinos()` → tabla `destinos`
3. `OnccaDestinosProductos::sincronizar()` → tabla `oncca_destino_producto`
4. `CuitEmpresas::CargarCuitEmpresas()` → tabla `cuit_empresas`

**Cuándo ejecutar:** Antes de `yii lector` para asegurar que los catálogos locales estén actualizados. El cron lo hace automáticamente: `yii parametros && yii lector`.

---

### `yii claves` — ClavesController

**Archivo:** `commands/ClavesController.php`  
**Propósito:** Gestión de claves/credenciales (probablemente encriptación de passwords de clientes).

---

### `yii test` — TestController

**Archivo:** `commands/TestController.php`  
**Propósito:** Pruebas manuales durante el desarrollo. No usar en producción.

---

### `yii test-excel` — TestExcelController

**Archivo:** `commands/TestExcelController.php`  
**Propósito:** Pruebas específicas del parser de Excel.

---

## Uso en producción

El cron ejecuta los comandos principales automáticamente:

```cron
*/15 * * * * cd /app && /usr/local/bin/php yii parametros && /usr/local/bin/php yii lector >> /var/log/cron.log 2>&1
```

Para ejecución manual (ej: debugging, reprocesamiento):

```bash
# Desde dentro del contenedor Docker
docker exec -it <container_name> bash
cd /app
php yii parametros
php yii lector
```

## Parámetros de configuración

Los comandos dependen de las variables de entorno definidas en `source/config/` (ver [configuracion.md](../07-operacion-y-despliegue/configuracion.md)):

- `DB_HOST`, `DB_NAME`, `DB_USER`, `DB_PASS` — conexión a MySQL
- Credenciales IMAP provienen de la tabla `cliente_configuracion`
- Credenciales Muvin API están hardcodeadas en `ServiciosMuvin.php` ⚠️
