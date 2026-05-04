# Endpoint: Países

> **Base URL:** `https://pruebas.muvinapp.com/api/backend/web/`

## GET `pais/select`

Retorna la lista de países disponibles para el selector de código de país en el login.

**Auth:** No requerida

**Response:**
```json
[
  { "id": 1, "nombre": "Argentina", "codigo": "54" },
  { "id": 2, "nombre": "Brasil", "codigo": "55" }
]
```

## Estado

> ⚠️ **No utilizado visualmente.** El método `obtenerPaises()` existe en `MuvinProvider` pero el selector de país en `LoginPage` está comentado. El código de país está hardcodeado a `'1'` (EE.UU.).
