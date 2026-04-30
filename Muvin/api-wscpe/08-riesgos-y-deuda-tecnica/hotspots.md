# Hotspots de Código

> [[_indice-flujos]] | [[deuda-tecnica]] | [[recomendaciones-modernizacion]]

Archivos con mayor densidad de riesgo o complejidad accidental.

## H-01 — `models/QueryCache.php` · `buscarCtg()` / `consultarCtg()`

**Riesgo:** Alto  
**Líneas:** ~116

El método combina en un solo bloque:
1. Búsqueda en caché (ActiveRecord)
2. Llamada AFIP (SOAP via `Afipws`)
3. Parseo de respuesta SOAP
4. Inserción en caché

**Problemas:**
- Sin TTL: un registro de caché nunca expira. Una CTG anulada en AFIP se devuelve como válida indefinidamente.
- `id_user` hardcodeado a `1` → no hay multi-tenancy.
- Sin manejo de transacción: si el INSERT al caché falla, la respuesta ya fue enviada al cliente.

---

## H-02 — `models/Certificado.php` · `descargarCertificado()`

**Riesgo:** Alto  
**Líneas:** ~79

Escribe `cert.key` y `key.pem` en disco en cada carga de certificado. Llamado desde `CertificadoController`.

**Problemas:**
- Sin lock de archivo → condición de carrera con requests concurrentes a `/cpe`.
- Sin rollback: BD actualizada antes de `file_put_contents`; si el write falla, BD y disco quedan inconsistentes.
- Ruta relativa (`Afip_res/`) depende del `cwd` del proceso PHP.

---

## H-03 — `components/Afipws.php` · `conectar()`

**Riesgo:** Medio  
**Función:** Obtiene el Ticket de Acceso desde WSAA o lee el `TA.xml` existente.

**Problemas:**
- `TA.xml` guardado en `Afip_TA/` sin lock → dos requests simultáneos pueden ambos intentar renovarlo.
- Passphrase leída directamente de BD en texto plano.
- Sin timeout configurado en `SoapClient` → un WSAA lento bloquea el proceso PHP indefinidamente.

---

## H-04 — `controllers/CpeController.php` · respuesta de error AFIP

**Riesgo:** Medio

Cuando AFIP devuelve error, la respuesta HTTP es `200 OK` con `success: true`:
```json
{ "success": true, "status": 200, "data": "Error: CUIT no habilitado" }
```
Los consumidores no pueden distinguir error de éxito sin parsear `data`.

---

## H-05 — `config/web.php` · `cookieValidationKey`

**Riesgo:** Alto  
Valor fijo versionado: `lFAP6jGr0BdmvFM3mzT48j6JSxO-iyG2`.  
Permite forjar cookies de sesión Yii2 en cualquier entorno que use este repo sin cambiar la clave.

---

## H-06 — `controllers/LoginController.php` · generación de token

**Riesgo:** Alto  
`md5(uniqid())` — `uniqid()` tiene entropía de microsegundos (predecible en entornos cloud).  
Reemplazar con `bin2hex(random_bytes(32))`.

---

## Resumen de hotspots

| ID | Archivo | Severidad | Fix estimado |
|----|---------|-----------|--------------|
| H-01 | `models/QueryCache.php` | 🔴 Alto | Agregar `cache_expires_at` + lógica TTL |
| H-02 | `models/Certificado.php` | 🔴 Alto | Transacción + file lock |
| H-03 | `components/Afipws.php` | 🟠 Medio | Lock + timeout SoapClient |
| H-04 | `controllers/CpeController.php` | 🟠 Medio | HTTP 4xx para errores AFIP |
| H-05 | `config/web.php` | 🔴 Alto | Variable de entorno |
| H-06 | `controllers/LoginController.php` | 🔴 Alto | `random_bytes(32)` |
