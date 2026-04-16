# Flujo: Turneada (Scheduling de Transporte)

> **Criticidad:** 🟡 Media
> **Módulos:** [[modulo-admin]], [[modulo-destino]], [[modulo-fertilizante]]
> **Tipo:** Sistema de turnos con máquina de estados
> **Punto de entrada UI:** Admin → Lista Turneada / Destino → Turnos

---

## Descripción funcional

La turneada es el sistema de asignación de turnos para la descarga de camiones en destino (planta). Cada centro configura su tipo de turneada (ninguna, tipo 1, tipo 2 con acción de rechazo). Se crean listas con grupos de transportistas. Cada turno tiene un estado que sigue una máquina de estados estricta con acciones como aceptar, rechazar, reprogramar, cancelar y reasignar.

---

## Configuración del tipo de turneada

```mermaid
flowchart LR
    CC["configurar-centro"]
    CC --> T0["tipo 0: Sin turneada"]
    CC --> T1["tipo 1: Turneada básica"]
    CC --> T2["tipo 2: Turneada con rechazo"]

    style T0 fill:#e0e0e0
    style T1 fill:#4caf50,color:#fff
    style T2 fill:#ff9800,color:#fff
```

El valor `id_tipo_turneada` se almacena en localStorage y determina el comportamiento del módulo turneada para el centro activo.

---

## Flujo principal

```mermaid
sequenceDiagram
    participant ADM as Admin/Centro
    participant LS as ListaTurneadaService
    participant API as Backend
    participant TRANSP as Transportista

    ADM->>API: POST lista-centro (crear lista)
    API-->>ADM: ListaTurneada creada

    ADM->>LS: Cargar turneada por fecha/filtros
    LS->>API: GET lista-centro/by-centro
    API-->>LS: ListaTurneada[] (con cupos + productos)

    LS->>LS: convertResponseTurneadaToLineal()<br/>Aplana a TableTurneada[]
    LS->>LS: convertResponseTurneadaDetalle()<br/>Genera ListaDetalleTurneada[]

    Note over LS: Determina isOwnerTurn por<br/>id_transporte == id_persona_transporte + ultimo

    ADM->>LS: Cambiar estado de turno
    LS->>LS: armarJsonCambioEstado()
    LS->>API: POST cambio-estado

    alt Estado → ACEPTADO
        TRANSP->>API: Confirma turno
        API-->>TRANSP: Genera viaje
    else Estado → RECHAZADO
        ADM->>LS: CANCELAR_RECHAZO → reasignar
    else Estado → REPROGRAMADO
        ADM->>LS: Asigna nueva fecha
    end
```

---

## Máquina de estados de la turneada

```mermaid
stateDiagram-v2
    [*] --> Pendiente

    Pendiente --> Aceptado: ASIGNAR
    Pendiente --> Rechazado: RECHAZAR
    Pendiente --> Cancelado: CANCELAR
    Pendiente --> Suspendido: SUSPENDER

    Aceptado --> Reprogramado: REPROGRAMAR (nueva fecha)
    Aceptado --> Arribado: Confirma arribo
    Aceptado --> Cancelado: CANCELAR

    Rechazado --> Pendiente: CANCELAR_RECHAZO → reasignar
    Cancelado --> Pendiente: ANULAR_CANCELACION

    Reprogramado --> Aceptado: ANULAR_REPROGRAMACION
    Reprogramado --> Cancelado: CANCELAR

    Aceptado --> ViajeCreado: Genera viaje
    ViajeCreado --> [*]: Todas las acciones deshabilitadas

    Devuelto --> [*]
    Arribado --> [*]
    Suspendido --> [*]
```

### Enum de estados

| ID | Estado | Constante |
|---|---|---|
| 1 | Pendiente | `PENDIENTE` |
| 2 | Aceptado | `ACEPTADO` |
| 3 | Rechazado | `RECHAZADO` |
| 4 | Devuelto | `DEVUELTO` |
| 5 | Suspendido | `SUSPENDIDO` |
| 6 | Cancelado | `CANCELADO` |
| 7 | Arribado | `ARRIBADO` |

---

## Endpoints involucrados

| Verbo | Ruta | Propósito | Servicio |
|---|---|---|---|
| GET | `lista-centro/by-centro` | Listas del centro por tipo turneada | CentrosService |
| GET | `lista-centro/select` | Dropdown de listas | CentrosService |
| GET | `lista-centro/chofer` | Listas del chofer | CentrosService |
| POST | `lista-centro` | Crear lista | CentrosService |
| POST | `lista-centro/duplicar?id_lista={id}` | Duplicar lista | CentrosService |
| PUT | `lista-centro/{id}` | Actualizar lista | CentrosService |
| GET | `turneada/control-viajes/{endPoint}` | Control de viajes (ferti) | FertilizantesService |
| GET | `turneada/lista/select?activa=1` | Listas activas | FertilizantesService |
| GET | `turneada/grupo/select` | Grupos de turneada | FertilizantesService |
| GET | `turneada/grupo-transporte/by-proveedor` | Grupos por proveedor | FertilizantesService |

---

## Estructuras de datos clave

### FiltroTurneada

```
{
  fecha_desde, fecha_hasta,   // Rango de fechas
  id_proveedor,               // Filtro por proveedor
  id_estado,                  // Estado del turno
  id_origen,                  // Origen de carga
  id_lista,                   // Lista específica
  id_destino,                 // Destino
  id_producto,                // Producto
  id_grupo,                   // Grupo de transporte
  id_transporte,              // Transportista específico
  con_viaje: boolean          // Solo con viaje generado
}
```

### CambioEstadoTurneada (payload)

```
{
  id_pedido,                  // Pedido vinculado
  id_lista,                   // Lista de turneada
  id_grupo,                   // Grupo dentro de la lista
  id_persona_transporte,      // Transportista
  estado                      // Nuevo estado (1-7)
}
```

### Grupo

```
{
  transportes[],              // Array de transportistas
  penalizado_hasta: date      // Fecha de penalización
}
```

---

## Penalización de grupos

Cada grupo puede ser penalizado con una fecha `penalizado_hasta`. El servicio `ListaTurneadaService` verifica esta fecha para mostrar penalizaciones en la UI e impedir acciones en grupos penalizados.

---

## Transformación de datos

```mermaid
flowchart LR
    A["Backend response<br/>ListaTurneada[]<br/>(con cupos + productos)"] -->|"convertResponseTurneadaToLineal()"| B["TableTurneada[]<br/>(filas planas para tabla)"]
    A -->|"convertResponseTurneadaDetalle()"| C["ListaDetalleTurneada[]<br/>(con estado/acción por transportista)"]
    C -->|"isOwnerTurn check"| D["UI renderiza<br/>quién tiene el turno"]
```

---

## Relación con otros flujos

- **[[flujo-pedido]]**: El viaje generado desde la turneada se vincula a un pedido
- **[[flujo-cupo]]**: Los cupos determinan qué pedidos entran a la turneada
- **[[flujo-autenticacion]]**: El `tipo_turneada` se almacena en localStorage durante el login

---

## Riesgos

| # | Sev. | Hallazgo |
|---|---|---|
| 1 | 🟡 | **Tipo de turneada en localStorage**: Si se manipula, puede desactivar el sistema de turnos |
| 2 | 🟡 | **Lógica de transformación compleja en el frontend**: 3 funciones de conversión con lógica de negocio que debería estar en backend |
| 3 | 🟡 | **Endpoints divididos**: turneada genérica en CentrosService, turneada fertilizante en FertilizantesService |

---

## Referencias

- [[_indice-flujos]] — Índice de flujos
- [[modulo-admin]] — Módulo Admin (lista-turneada)
- [[modulo-fertilizante]] — Módulo Fertilizante (turneada ferti)
- [[centros-endpoints]] — Endpoints CentrosService
- [[fertilizantes-endpoints]] — Endpoints FertilizantesService
