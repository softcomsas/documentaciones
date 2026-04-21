# Endpoints: Fertilizantes y MAGyP

> **Servicios:** `FertilizantesService`, `HorarioFertilizantesService`, `MagypService`
> **API:** `GlobalService.apiHost`
> **Consumido por:** [[modulo-fertilizante]], [[modulo-magyp]]

---

## FertilizantesService

> **Archivo:** `fertilizantes.service.ts` · ~15 endpoints

### Operaciones de fertilizantes

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `fertilizante` | Listar fertilizantes (paginado) |
| GET | `fertilizante/{id}` | Detalle |
| GET | `fertilizante/producto` | Productos fertilizantes |
| GET | `fertilizante/lote` | Lotes de fertilizante |
| POST | `fertilizante` | Crear fertilizante |
| POST | `fertilizante/lote` | Crear lote |
| PUT | `fertilizante/{id}` | Actualizar fertilizante |
| PUT | `fertilizante/lote/{id}` | Actualizar lote |
| DELETE | `fertilizante/{id}` | Eliminar fertilizante |

### Cupos de fertilizantes

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `fertilizante/cupos` | Cupos de fertilizantes |
| GET | `fertilizante/cupos/disponibles` | Cupos disponibles |
| POST | `fertilizante/cupos/asignar` | Asignar cupo de fertilizante |

---

## HorarioFertilizantesService {#horarios}

> **Archivo:** `horario-fertilizantes.service.ts` · ~6 endpoints

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `horario-fertilizante` | Listar horarios |
| GET | `horario-fertilizante/{id}` | Detalle |
| POST | `horario-fertilizante` | Crear horario |
| PUT | `horario-fertilizante/{id}` | Actualizar horario |
| DELETE | `horario-fertilizante/{id}` | Eliminar horario |

---

## MagypService

> **Archivo:** `magyp.service.ts` · ~16 endpoints

### Cadenas y contactos

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `magyp/cadena` | Cadenas MAGyP |
| GET | `magyp/cadena/{id}` | Detalle de cadena |
| GET | `magyp/contacto` | Contactos MAGyP |
| GET | `magyp/contacto/{id}` | Detalle de contacto |
| POST | `magyp/contacto` | Crear contacto |
| PUT | `magyp/contacto/{id}` | Actualizar contacto |

### Consultas y resoluciones MAGyP

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `magyp/consulta` | Consultas MAGyP |
| GET | `magyp/resolucion` | Resoluciones |
| POST | `magyp/consulta` | Crear consulta |
| PUT | `magyp/consulta/{id}` | Responder consulta |

### Reportes MAGyP

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `magyp/reporte` | Reportes |
| GET | `magyp/estadistica` | Estadísticas |

---

## Archivos fuente

- `src/app/shared/services/fertilizantes.service.ts`
- `src/app/shared/services/horario-fertilizantes.service.ts`
- `src/app/shared/services/magyp.service.ts`

---

## Referencias

- [[_indice-servicios]] — Índice general
- [[modulo-fertilizante]] — Módulo fertilizantes
- [[modulo-magyp]] — Módulo MAGyP
