# Endpoints — Módulo Viterra

> **Controlador:** `modules/viterra/controllers/DestinoController.php`
> **Base URL:** `/viterra/destino/`
> **Autenticación:** JWT Bearer
> **Particularidad:** Única integración basada en **base de datos local** (no HTTP externo).

---

## GET `/viterra/destino/situacion-puerto`

**Descripción:** Retorna la situación actual de turnos y horarios en el puerto Viterra. Consulta la base de datos local que Viterra sincroniza externamente.

**Flujo:**

```mermaid
flowchart LR
    A[Cliente] -->|JWT + query params| B[DestinoController\nsituacionPuerto]
    B --> C[SituacionTurnos Form\nvalidación]
    C --> D[HorarioPuerto::find\nActiveRecord query]
    D --> E[(Base de datos\nViterra)]
    E --> D
    D --> B
    B -->|{success, status, data}| A
```

**Query Params:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `fecha` | date | Sí | Fecha a consultar (YYYY-MM-DD) |
| `producto` | string | No | Filtrar por producto |
| `origen` | string | No | Filtrar por origen |

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": [
    {
      "id_horario": 123,
      "fecha": "2024-01-15",
      "hora_inicio": "08:00",
      "hora_fin": "09:00",
      "cupos_disponibles": 5,
      "producto": "SOJA",
      "origen": "CÓRDOBA"
    }
  ]
}
```

---

## GET `/viterra/destino/informacion-ventanilla/{id_horario}`

**Descripción:** Retorna información detallada de una ventanilla (slot de turno) específica, dado su ID de horario.

**Path Params:**

| Param | Tipo | Descripción |
|-------|------|-------------|
| `id_horario` | integer | ID del horario/ventanilla |

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "id_horario": 123,
    "ventanilla": "A",
    "turno": "MAÑANA",
    "camiones": [...],
    "productos": [...]
  }
}
```

---

## Modelos utilizados

| Modelo | Tabla | Descripción |
|--------|-------|-------------|
| `HorarioPuerto` | `horario_puerto` | Horarios disponibles |
| `TurnoPuerto` | `turno_puerto` | Turnos asignados |
| `Cupo` | `cupo` | Cupos por horario |
| `Ventanilla` | `ventanilla` | Ventanillas de carga |
| `Producto` | `producto` | Productos habilitados |
| `OrigenDestino` | `origen_destino` | Orígenes/destinos |
| `Camion` | `camion` | Camiones registrados |
| `Personas` | `personas` | Choferes/personas |

---

## Referencias

- [[modulo-viterra]]
- [[f05-viterra-situacion-puerto]]
- [[entidades-viterra]]
