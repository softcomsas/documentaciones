# Endpoint: CPE

> [[_indice-servicios]] | Módulo: [[modulo-cpe]]

## POST `/cpe`

**Auth:** ✅ `Authorization: Bearer {token}`

**Request:**
```json
{
  "cuit": "20123456789",
  "ctg": 123456789
}
```

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `cuit` | string | ✅ | CUIT del interviniente a consultar (11 dígitos) |
| `ctg` | integer | ✅ | Número de CTG de la CPE |

**Response 200 — CPE encontrada:**
```json
{
  "success": true,
  "status": 200,
  "data": [
    {
      "nroCPE": "0001-00000001",
      "estado": "AC",
      "fechaEmision": "2024-01-10",
      "fechaVencimiento": "2024-01-15",
      "titular": {
        "cuit": "20123456789",
        "razonSocial": "Empresa S.A."
      },
      "destinatario": {
        "cuit": "27987654321",
        "razonSocial": "Destino S.R.L.",
        "planta": "1",
        "localidad": "Rosario"
      },
      "mercaderia": {
        "codigoEspecie": "23",
        "descripcionEspecie": "Soja",
        "cosecha": "2023/2024",
        "cantidad": 30000,
        "unidad": "KG"
      },
      "transporte": {
        "patente": "ABC123",
        "chofer": "Juan Pérez",
        "kmRecorridos": 250
      }
    }
  ]
}
```

> ⚠️ La estructura exacta del objeto CPE depende del tipo de CPE devuelto por AFIP. El campo `pdf` es eliminado antes de responder.

**Response 200 — CUIT no delegado:**
```json
{
  "success": true,
  "status": 200,
  "data": ["El cuit 20123456789 no se puede consultar (no delegado)"]
}
```

**Response 200 — Error AFIP:**
```json
{
  "success": true,
  "status": 200,
  "data": ["Error en el WEBSERVICE - SoapFault: ..."]
}
```

> ⚠️ Los errores de AFIP devuelven HTTP 200 con `success: true`. Verificar el contenido de `data` para detectar errores.

**Response 401 — Sin token o token inválido:**
```json
{
  "success": false,
  "status": 401,
  "data": "Your request was made with invalid credentials."
}
```
