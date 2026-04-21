# Endpoints: Logística (Destinos, Vehículos, Operaciones)

> **Servicios:** `DestinosService`, `CamionService`, `AcopladosService`, `EquiposService`, `ChoferService`, `SituacionPuertoService`, `HorarioPuertoService`, `ListaTurneadaService`, `DestinatarioService`, `TransporteChoferService`, `ReservasService`, `ConsultasService`, `PlayasIntermediasService`, `DocumentacionService`
> **API:** `apiHost` (mayoría) + `URL_SERVICIOS` (turneadas, consultas)
> **Consumido por:** [[modulo-destino]], [[modulo-admin]], [[modulo-cupo]], [[modulo-cupera]]

---

## DestinosService {#destinos}

> **Archivo:** `destinos.service.ts` · ~400 líneas · ~21 endpoints

### CRUD Destino

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `destino` | Listar destinos (paginado) |
| GET | `destino/select` | Dropdown de destinos |
| GET | `destino/{id}` | Detalle de destino |
| POST | `destino` | Crear destino |
| PUT | `destino/{id}` | Actualizar destino |
| DELETE | `destino/{id}` | Eliminar destino |

### Turnos y horarios

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `destino/turnos` | Turnos del destino |
| GET | `destino/turnos/{fecha}` | Turnos por fecha |
| POST | `destino/turnos` | Crear turno |
| PUT | `destino/turnos/{id}` | Actualizar turno |

### Dashboard

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `v3/cupo/destinados/{fecha}` | Cupos destinados por fecha (v3) |
| GET | `destino/producto-destino` | Productos por destino |

---

## SituacionPuertoService {#situacion-puerto}

> **Archivo:** `situacion-puerto.service.ts` · ~6 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `situacion-puerto` | Estado actual del puerto |
| GET | `situacion-puerto/{id}` | Detalle de situación |
| POST | `situacion-puerto` | Crear situación |
| PUT | `situacion-puerto/{id}` | Actualizar situación |

---

## HorarioPuertoService {#horario-puerto}

> **Archivo:** `horario-puerto.service.ts` · ~6 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `horario-puerto` | Horarios del puerto |
| GET | `horario-puerto/{id}` | Detalle de horario |
| POST | `horario-puerto` | Crear horario |
| PUT | `horario-puerto/{id}` | Actualizar horario |
| DELETE | `horario-puerto/{id}` | Eliminar horario |

---

## CamionService {#camion}

> **Archivo:** `camion.service.ts` · ~70 líneas · 8 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `camion` | Listar camiones (paginado) |
| GET | `camion/patente-existe` | Verificar si patente existe |
| GET | `camion/get-one-by-patente` | Buscar por patente |
| GET | `camion/sin-equipo` | Camiones sin equipo asignado |
| POST | `camion` | Crear camión |
| PUT | `camion/{id}` | Actualizar camión |
| PUT | `camion/{id}/lock` | Bloquear camión |
| DELETE | `camion/{id}` | Eliminar camión |

---

## AcopladosService {#acoplado}

> **Archivo:** `acoplados.service.ts` · ~89 líneas · 8 endpoints

Espejo de `CamionService` para acoplados (remolques):

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `acoplado` | Listar acoplados (paginado) |
| GET | `acoplado/patente-existe` | Verificar si patente existe |
| GET | `acoplado/get-one-by-patente` | Buscar por patente |
| GET | `acoplado/sin-equipo` | Acoplados sin equipo |
| POST | `acoplado` | Crear acoplado |
| PUT | `acoplado/{id}` | Actualizar acoplado |
| PUT | `acoplado/{id}/lock` | Bloquear acoplado |
| DELETE | `acoplado/{id}` | Eliminar acoplado |

---

## EquiposService {#equipo}

> **Archivo:** `equipos.service.ts` · ~110 líneas · 7 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `equipo/equipo-transporte` | Listar equipos de transporte |
| GET | `equipo/{id}` | Detalle de equipo |
| POST | `equipo` | Crear equipo (camión + acoplado) |
| PUT | `equipo/{id}` | Actualizar equipo |
| PUT | `equipo/{id}/lock` | Bloquear equipo |
| PUT | `equipo/{id}/unlock` | Desbloquear equipo |
| DELETE | `equipo/{id}` | Eliminar equipo |

---

## ChoferService {#chofer-asignar}

> **Archivo:** `chofer.service.ts` · ~30 líneas · 2 endpoints

Servicio ultra-liviano. Solo asignación:

| Verbo | Ruta | Propósito |
|---|---|---|
| POST | `pedido/asignar-actualizar-chofer` | Asignar/actualizar chofer a pedido |
| POST | `pedido/set-intermediario` | Asignar intermediario a pedido |

---

## TransporteChoferService {#transporte-chofer}

> **Archivo:** `transporte-chofer.service.ts` · ~8 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `transporte-chofer` | Listar relaciones transporte-chofer |
| GET | `transporte-chofer/{id}` | Detalle |
| POST | `transporte-chofer` | Crear relación |
| PUT | `transporte-chofer/{id}` | Actualizar |
| DELETE | `transporte-chofer/{id}` | Eliminar |

---

## DestinatarioService {#destinatario}

> **Archivo:** `destinatario.service.ts` · ~8 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `destinatario` | Listar destinatarios |
| GET | `destinatario/{id}` | Detalle |
| POST | `destinatario` | Crear |
| PUT | `destinatario/{id}` | Actualizar |
| DELETE | `destinatario/{id}` | Eliminar |

---

## ListaTurneadaService {#lista-turneada}

> **Archivo:** `lista-turneada.service.ts` · ~8 endpoints
> **API:** `URL_SERVICIOS` (v3)

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `v3/turneada/lista` | Lista de turneada |
| GET | `v3/turneada/estado` | Estado actual de turneada |
| POST | `v3/turneada/iniciar` | Iniciar turneada |
| PUT | `v3/turneada/finalizar` | Finalizar turneada |
| PUT | `v3/turneada/cancelar` | Cancelar turneada |

---

## ReservasService {#reservas}

> **Archivo:** `reservas.service.ts` · ~8 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `reserva` | Listar reservas |
| GET | `reserva/{id}` | Detalle de reserva |
| POST | `reserva` | Crear reserva |
| PUT | `reserva/{id}` | Actualizar reserva |
| PUT | `reserva/{id}/aprobar` | Aprobar reserva |
| PUT | `reserva/{id}/rechazar` | Rechazar reserva |
| POST | `reserva/derivar` | Derivar reserva a otro centro |
| DELETE | `reserva/{id}` | Eliminar reserva |

---

## ConsultasService {#consultas}

> **Archivo:** `consultas.service.ts` · ~6 endpoints
> **API:** `URL_SERVICIOS` (v3)

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `v3/consulta` | Listado de consultas |
| GET | `v3/consulta/{id}` | Detalle de consulta |
| POST | `v3/consulta` | Crear consulta |
| PUT | `v3/consulta/{id}` | Responder consulta |

---

## PlayasIntermediasService {#playas}

> **Archivo:** `playas-intermedias.service.ts` · ~4 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `playa-intermedia` | Listar playas |
| GET | `playa-intermedia/{id}` | Detalle |
| POST | `playa-intermedia` | Crear |
| PUT | `playa-intermedia/{id}` | Actualizar |

---

## DocumentacionService {#documentacion}

> **Archivo:** `documentacion.service.ts` · ~5 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `documentacion/centro/{id}` | Documentación requerida por centro |
| GET | `documentacion/chofer/{id}` | Documentación del chofer |
| POST | `documentacion/upload` | Subir documento |
| PUT | `documentacion/{id}` | Actualizar documento |

---

## Archivos fuente

- `src/app/shared/services/destinos.service.ts`
- `src/app/shared/services/situacion-puerto.service.ts`
- `src/app/shared/services/horario-puerto.service.ts`
- `src/app/shared/services/camion.service.ts`
- `src/app/shared/services/acoplados.service.ts`
- `src/app/shared/services/equipos.service.ts`
- `src/app/shared/services/chofer.service.ts`
- `src/app/shared/services/transporte-chofer.service.ts`
- `src/app/shared/services/destinatario.service.ts`
- `src/app/shared/services/lista-turneada.service.ts`
- `src/app/shared/services/reservas.service.ts`
- `src/app/shared/services/consultas.service.ts`
- `src/app/shared/services/playas-intermedias.service.ts`
- `src/app/shared/services/documentacion.service.ts`

---

## Referencias

- [[_indice-servicios]] — Índice general
- [[modulo-destino]] — Consumidor principal de DestinosService
- [[centros-endpoints]] — Vinculaciones con centros
