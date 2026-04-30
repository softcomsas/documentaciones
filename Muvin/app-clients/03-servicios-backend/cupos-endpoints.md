# Endpoints de Cupos

> [[_indice-servicios]] | Módulo: [[modulo-cupos]]

## GET `v3/cupo/disponibles`

Cupos disponibles para perfil Cliente Final.

**Query params:** `userId`, `fecha` (YYYY-MM-DD)

**Response:** array de objetos cupo con `id`, `producto`, `cantidad`, `estado`, `cabecera`.

**BLoC:** `SolicitudCupoBloc.getCuposDisponibles()`

---

## GET `v3/cupo/listado`

Cupos disponibles para perfil Admin/Dador.

**Query params:** `fecha`, `cabecera`, `ccpp`

**Response:** array de cupos con disponibilidad y demanda.

**BLoC:** `SolicitudCupoBloc.getCuposListado()`

---

## GET `v2/cupos/buscar-x-demandante`

> 💀 Endpoint legacy v2. Verificar migración.

Busca cupos filtrados por nombre/CUIT de demandante.

**Query params:** `q` (texto de búsqueda)

**Response:** array de coincidencias.

**BLoC:** `BusquedaBloc`

---

## POST `v3/cupo/asignar`

Asigna un cupo a un demandante.

**Body:**
```json
{
  "cupoId": 100,
  "demandanteId": 42,
  "cantidad": 50
}
```

**BLoC:** `SolicitudCupoBloc.asignarCupo()`

---

## POST `v3/cupo/asignar2`

Variante de asignación (usada en flujos específicos del Admin). Mismo esquema de body que `asignar`.

---

## POST `v3/cupo/recuperar`

Recupera (revierte) un cupo asignado.

**Body:**
```json
{
  "cupoId": 100,
  "motivo": "Error de carga"
}
```

**BLoC:** `SolicitudCupoBloc.recuperarCupo()`

---

## POST `v3/cupo/carga-solicitud`

Crea una solicitud de carga simple.

**Body:**
```json
{
  "cupoId": 100,
  "demandanteId": 42,
  "cantidad": 30,
  "observaciones": ""
}
```

---

## POST `v3/cupo/carga-solicitud-distribuida`

Solicitud de carga distribuida entre múltiples demandantes.

**Body:** similar a `carga-solicitud` con array de distribuciones.

---

## GET `v2/cupos/demandados/:fecha`

> 💀 Endpoint legacy v2.

Solicitudes del usuario para una fecha específica.

**Path param:** `fecha` (YYYY-MM-DD)

**BLoC:** `SolicitudCupoBloc.getSolicitudesPropias()`

---

## GET `demanda-cupo/by-demandante`

Demandas del demandante en un rango de fechas.

**Query params:** `desde`, `hasta` (YYYY-MM-DD)

**Response:** array de `SolicitudCardModel`.

---

## POST `demanda-cupo/variar-cantidad`

Modifica la cantidad de una demanda pendiente.

**Body:**
```json
{
  "demandaId": 55,
  "cantidad": 45.5
}
```

**BLoC:** `ModificarCantCupoBloc`

---

## PUT `v3/cupo/:id`

Asocia un entregador a un cupo existente.

**Path param:** `id` del cupo

**Body:**
```json
{
  "entregadorId": 99
}
```
