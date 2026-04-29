# Modelo de Datos — api-bus

> **Última revisión:** 2026-04-29

api-bus es principalmente un proxy de integración y **no posee un modelo de datos propio extenso**. Sin embargo, mantiene dos conjuntos de entidades:

1. **`SanMiguelKey`** — Tabla propia: almacena credenciales/tokens para el GPS SanMiguel.
2. **Modelos Viterra (12 entidades)** — ActiveRecord que mapea la base de datos del sistema Viterra (leída, no escrita por api-bus).

---

## Diagrama ER Global

```mermaid
erDiagram
    %% === TABLA PROPIA ===
    agroq_san_miguel_key {
        int id PK
        varchar(255) key
    }

    %% === ESQUEMA VITERRA (read-only) ===
    horario_puerto {
        int id PK
        date fecha
        time hora_inicio
        time hora_fin
        int id_producto FK
        int id_origen_destino FK
    }

    turno_puerto {
        int id PK
        int id_horario FK
        int id_camion FK
        int id_chofer FK
        varchar estado
    }

    cupo {
        int id PK
        int id_horario FK
        int cantidad_total
        int cantidad_usada
    }

    ventanilla {
        int id PK
        int id_horario FK
        varchar nombre
        varchar estado
    }

    producto {
        int id PK
        varchar nombre
        varchar codigo
    }

    origen_destino {
        int id PK
        varchar nombre
        varchar tipo
    }

    camion {
        int id PK
        varchar patente
        int id_acoplado FK
        int id_equipo FK
    }

    acoplado {
        int id PK
        varchar patente
    }

    equipo {
        int id PK
        varchar descripcion
        int id_chofer_equipo FK
    }

    chofer_equipo {
        int id PK
        int id_persona FK
    }

    personas {
        int id PK
        varchar nombre
        varchar apellido
        varchar cuit
    }

    persona_rol {
        int id PK
        int id_persona FK
        varchar rol
    }

    %% === RELACIONES ===
    horario_puerto ||--o{ turno_puerto : "tiene"
    horario_puerto ||--o{ cupo : "tiene"
    horario_puerto ||--o{ ventanilla : "tiene"
    horario_puerto }o--|| producto : "es de"
    horario_puerto }o--|| origen_destino : "pertenece a"
    turno_puerto }o--|| camion : "asignado a"
    turno_puerto }o--|| personas : "chofer"
    camion }o--o| acoplado : "lleva"
    camion }o--o| equipo : "tiene"
    equipo }o--o| chofer_equipo : "asignado"
    chofer_equipo }o--|| personas : "es"
    personas ||--o{ persona_rol : "tiene"
```

---

## Resumen de entidades

| Entidad | Tabla | Módulo | Tipo | Descripción |
|---------|-------|--------|------|-------------|
| `SanMiguelKey` | `agroq_san_miguel_key` | agroquimicos | Propia (R/W) | Tokens GPS SanMiguel |
| `HorarioPuerto` | `horario_puerto` | viterra | Externa (R) | Franjas horarias de carga |
| `TurnoPuerto` | `turno_puerto` | viterra | Externa (R) | Turnos asignados |
| `Cupo` | `cupo` | viterra | Externa (R) | Disponibilidad por horario |
| `Ventanilla` | `ventanilla` | viterra | Externa (R) | Ventanillas de carga |
| `Producto` | `producto` | viterra | Externa (R) | Granos/productos |
| `OrigenDestino` | `origen_destino` | viterra | Externa (R) | Orígenes y destinos |
| `Camion` | `camion` | viterra | Externa (R) | Vehículos registrados |
| `Acoplado` | `acoplado` | viterra | Externa (R) | Acoplados de camiones |
| `Equipo` | `equipo` | viterra | Externa (R) | Equipos de trabajo |
| `ChoferEquipo` | `chofer_equipo` | viterra | Externa (R) | Relación chofer-equipo |
| `Personas` | `personas` | viterra | Externa (R) | Personas/choferes |
| `PersonaRol` | `persona_rol` | viterra | Externa (R) | Roles de personas |

---

## Archivos de detalle

- [[entidad-san-miguel-key]]
- [[entidades-viterra]]
