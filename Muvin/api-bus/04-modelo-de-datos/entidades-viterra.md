# Entidades Viterra

> **Módulo:** `viterra`
> **Ubicación modelos:** `modules/viterra/models/`
> **Tipo:** ActiveRecord Yii 2 — base de datos **externa** gestionada por Viterra (solo lectura desde api-bus)

---

## Descripción general

El módulo Viterra no se integra vía HTTP con un sistema externo, sino que **lee directamente de una base de datos relacional** que el sistema Viterra sincroniza y gestiona. api-bus actúa como capa de exposición REST de esos datos.

Hay **12 modelos ActiveRecord** que mapean las tablas del esquema Viterra.

---

## Modelos

### HorarioPuerto

**Tabla:** `horario_puerto`

Representa una franja horaria disponible para carga en el puerto.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `fecha` | date | Fecha del horario |
| `hora_inicio` | time | Inicio de la franja |
| `hora_fin` | time | Fin de la franja |
| `id_producto` | int FK | Producto habilitado |
| `id_origen_destino` | int FK | Origen/destino |

**Usado en:** `DestinoController::situacionPuerto()`

---

### TurnoPuerto

**Tabla:** `turno_puerto`

Turno asignado a un camión en un horario específico.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `id_horario` | int FK | Horario asignado |
| `id_camion` | int FK | Camión |
| `id_chofer` | int FK → Personas | Chofer del turno |
| `estado` | varchar | Estado del turno |

---

### Cupo

**Tabla:** `cupo`

Disponibilidad de cupos por horario.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `id_horario` | int FK | Horario relacionado |
| `cantidad_total` | int | Cupos totales |
| `cantidad_usada` | int | Cupos ya ocupados |

---

### Ventanilla

**Tabla:** `ventanilla`

Ventanilla de carga asignada a un horario.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `id_horario` | int FK | Horario |
| `nombre` | varchar | Nombre/código de ventanilla |
| `estado` | varchar | Estado (libre, ocupada, etc.) |

---

### Producto

**Tabla:** `producto`

Catálogo de granos/productos habilitados.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `nombre` | varchar | Nombre del producto |
| `codigo` | varchar | Código interno |

---

### OrigenDestino

**Tabla:** `origen_destino`

Orígenes o destinos habilitados para los turnos.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `nombre` | varchar | Nombre del origen/destino |
| `tipo` | varchar | `origen` o `destino` |

---

### Camion

**Tabla:** `camion`

Vehículos registrados en el sistema.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `patente` | varchar | Patente del camión |
| `id_acoplado` | int FK | Acoplado asociado |
| `id_equipo` | int FK | Equipo asignado |

---

### Acoplado

**Tabla:** `acoplado`

Remolques/acoplados de camiones.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `patente` | varchar | Patente del acoplado |

---

### Equipo

**Tabla:** `equipo`

Equipos de trabajo asignados a camiones.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `descripcion` | varchar | Descripción del equipo |
| `id_chofer_equipo` | int FK | Chofer principal del equipo |

---

### ChoferEquipo

**Tabla:** `chofer_equipo`

Relación entre choferes y equipos.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `id_persona` | int FK | Persona/chofer |

---

### Personas

**Tabla:** `personas`

Directorio de personas registradas (choferes principalmente).

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `nombre` | varchar | Nombre |
| `apellido` | varchar | Apellido |
| `cuit` | varchar | CUIT |

---

### PersonaRol

**Tabla:** `persona_rol`

Roles asignados a personas.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int PK | Identificador |
| `id_persona` | int FK | Persona |
| `rol` | varchar | Rol (chofer, supervisor, etc.) |

---

## Consideraciones

> ⚠️ **api-bus NO escribe en el esquema Viterra.** Todos los modelos son de solo lectura.
> La base de datos es gestionada externamente por el sistema Viterra y replicada/accesible en la instancia donde corre api-bus.

---

## Referencias

- [[modulo-viterra]]
- [[viterra-endpoints]]
- [[_indice-entidades]]
