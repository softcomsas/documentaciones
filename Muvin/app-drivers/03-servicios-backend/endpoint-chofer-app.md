# Endpoints: Chofer App

> **Base URL:** `https://pruebas.muvinapp.com/api/backend/web/`
> **Auth:** `Authorization: Bearer {token}` en todos los endpoints de esta sección.

---

## GET `chofer-app/pedidos-publicos`

Retorna la lista de pedidos de transporte disponibles para postularse.

**Response:**
```json
{
  "data": [
    {
      "id": 42,
      "nombreDestino": "Buenos Aires",
      "fechaCarga": "2024-03-15",
      "tipoCarga": "Granel",
      "peso": 20000,
      "distancia": 450,
      "postulado": false
    }
  ]
}
```

---

## GET `chofer-app/postulaciones`

Retorna los pedidos a los que el chofer ya se postuló.

**Response:** mismo shape que `pedidos-publicos` con `postulado: true`.

---

## GET `chofer-app/mi-viaje`

Retorna el viaje actualmente asignado al chofer, si existe.

**Response con viaje:**
```json
{
  "id": 42,
  "origen": "Rosario",
  "destino": "Buenos Aires",
  "pdf": "https://...",
  "estado": "en_camino"
}
```

**Response sin viaje:**
```json
null
```

> El campo `pdf` es el que habilita el botón en `HomePage`.

---

## POST `chofer-app/postularme?id_pedido={id}`

Registra la postulación del chofer al pedido indicado.

**Query param:** `id_pedido` (integer)
**Body:** vacío

**Response exitoso:**
```json
{ "ok": true }
```

---

## POST `chofer-app/des-postularme?id_pedido={id}`

Cancela la postulación del chofer.

**Query param:** `id_pedido` (integer)
**Body:** vacío

---

## POST `chofer-app/actualizar-posicion`

Actualiza la posición GPS del chofer. Llamado cada 10 segundos desde `HomePage`.

**Request:**
```json
{
  "latitud": -34.6037,
  "longitud": -58.3816
}
```

---

## POST `chofer-app/subir-remito-inicial`

Sube la foto del remito de carga.

**Content-Type:** `multipart/form-data`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `archivo` | File | Imagen del remito |
| `tipo` | String | `"carta"` |

---

## POST `chofer-app/subir-remito-final`

Sube la foto del remito de entrega.

**Content-Type:** `multipart/form-data`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `archivo` | File | Imagen del remito |
| `tipo` | String | `"descarga"` |
