# Endpoints de Catálogos

> [[_indice-servicios]]

## GET `v3/cabecera/select`

Listado de cabeceras operacionales disponibles para el usuario. Usado como dropdown en filtros de cupos.

**Response:**
```json
[
  { "id": 1, "nombre": "Cabecera Norte" },
  { "id": 2, "nombre": "Cabecera Sur" }
]
```

**BLoC:** `CabeceraBloc`

---

## GET `v3/ccpp/:id`

Datos del Centro de Carga (CCPP) asociado a una cabecera.

**Path param:** `id` de la cabecera

**Response:** objeto con `id`, `nombre`, `localidad`, coordenadas.

---

## GET `alfanumerico`

Valores de catálogos alfanuméricos genéricos (ej. tipos de producto, estados, etc).

**Query params:** `tipo` (código del catálogo)

**Response:** array de `{id, codigo, descripcion}`.

**Modelo:** [AlfanumericoModel](../04-modelo-de-datos/alfanumerico-model.md)
