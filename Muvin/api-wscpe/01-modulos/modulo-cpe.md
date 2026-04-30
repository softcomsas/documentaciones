# Módulo: CpeController

> **Archivo:** `controllers/CpeController.php`
> **Ruta base:** `/cpe`
> **Auth:** ✅ Bearer token
> **Criticidad:** 🔴 Crítica — es la función principal del servicio

## Propósito

Único endpoint operacional del servicio. Recibe un CUIT y un CTG, consulta la CPE en AFIP (con caché) y devuelve los datos estructurados de la Carta de Porte Electrónica.

## Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionIndex` | POST | `/cpe` | Consultar CPE por CUIT + CTG |

## actionIndex — Detalle

**Request:**
```json
{
  "cuit": "20123456789",
  "ctg": 123456789
}
```

**Lógica:**
1. Extrae `cuit` y `ctg` del body (ambos sanitizados por `purificar()` en `beforeRequest`).
2. Llama a `QueryCache::buscarCtg($ctg, $cuit, 1)`.
3. `QueryCache` busca en BD si ya existe un resultado para ese par `(ctg, cuit_consulta)`.
4. Si existe Y el CUIT está en los datos del resultado → devuelve cache.
5. Si no → llama a AFIP vía `Afipws::consultarCtg()` → guarda en cache → devuelve.

**Response exitosa (200):**
```json
{
  "success": true,
  "status": 200,
  "data": [
    {
      "nroCPE": "...",
      "estado": "...",
      "fechaEmision": "...",
      "titular": { "cuit": "...", "razonSocial": "..." },
      "destinatario": { ... },
      "mercaderia": { "codigoEspecie": "...", "cantidad": ... },
      "transporte": { ... }
    }
  ]
}
```
> Nota: El campo `pdf` de la respuesta AFIP es eliminado antes de devolver (`unset($response->pdf)`).

**Response errores:**
```json
{ "success": false, "status": 200, "data": ["El cuit 20... no se puede consultar (no delegado)"] }
{ "success": false, "status": 200, "data": ["Error en el WEBSERVICE - ..."] }
```

## Dependencias

- `QueryCache` → caché en BD + llamada a AFIP
- `Afipws` → cliente SOAP WSCPE
- `Certificado` → certificado del usuario `id_user=1` (hardcodeado)

## Riesgos

- ⚠️ El `id_user` está hardcodeado a `1`. Solo puede operar con el certificado del usuario administrador. No hay multi-tenancy.
- ⚠️ La caché no tiene TTL. Una CPE consultada queda almacenada para siempre. Si la CPE cambia de estado en AFIP (ej. se anula), la app devuelve el estado viejo.
- ⚠️ El manejo de errores de AFIP devuelve HTTP 200 con el mensaje de error en `data`. Dificulta el manejo de errores en los consumidores.
