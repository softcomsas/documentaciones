# Flujo: Derivación de Reservas

> **Criticidad:** 🟡 Media
> **Módulos:** [[modulo-fertilizante]] (SharedModule)
> **Tipo:** Flujo de redistribución de reservas entre proveedores
> **Punto de entrada UI:** Fertilizante → Reservas → Derivar

---

## Descripción funcional

La derivación de reservas permite a un proveedor de fertilizantes reasignar una o más reservas a otro proveedor. El usuario selecciona reservas, elige el proveedor destino, la terminal y el stock de origen, y ejecuta la derivación. El componente vive en SharedModule (no en FertilizanteModule) porque puede ser invocado desde diferentes contextos.

---

## Actores involucrados

```mermaid
graph LR
    PROV_A["🧑‍💼 Proveedor origen<br/>(tiene reservas)"]
    PROV_B["🧑‍💼 Proveedor destino<br/>(recibe reservas)"]
    SISTEMA["⚙️ DerivarReservaComponent"]
    API["🌐 Backend API"]

    PROV_A -->|"Selecciona reservas"| SISTEMA
    SISTEMA -->|"Consulta proveedores"| API
    SISTEMA -->|"Consulta terminales + stock"| API
    SISTEMA -->|"Ejecuta derivación"| API
    API -->|"Transfiere"| PROV_B
```

---

## Flujo paso a paso

```mermaid
sequenceDiagram
    participant U as Usuario (Proveedor A)
    participant DRC as DerivarReservaComponent
    participant FS as FertilizantesService
    participant RS as ReservasService
    participant API as Backend

    Note over U,DRC: Reservas seleccionadas previamente (IDs)

    DRC->>FS: Obtener proveedores para derivar
    FS->>API: GET fertilizantes/proveedor/select?id_reserva={id}
    API-->>FS: Proveedor[] (destinos posibles)
    FS-->>DRC: Lista de proveedores

    U->>DRC: Selecciona proveedor destino

    DRC->>FS: Obtener terminales del proveedor
    FS->>API: GET fertilizantes/proveedor/terminales?id_proveedor={id}
    API-->>FS: Terminal[]
    FS-->>DRC: Lista de terminales

    U->>DRC: Selecciona terminal

    DRC->>FS: Obtener stock disponible
    FS->>API: GET fertilizantes/proveedor/mi-reserva-stock?id={id}
    API-->>FS: ReservaStock[]
    FS-->>DRC: Stock disponible

    U->>DRC: Selecciona stock y confirma

    DRC->>RS: Ejecutar derivación
    RS->>API: POST fertilizantes/reserva/derivar
    Note over API: Payload: {reservas: [ids],<br/>id_proveedor, id_origen,<br/>id_stock, contrata: 0|1}
    API-->>RS: OK (reservas derivadas)
    RS-->>DRC: Éxito → cierra dialog
```

---

## Flujo de decisiones

```mermaid
flowchart TD
    A([Inicio: Reservas seleccionadas]) --> B["Consulta proveedores destino<br/>GET fertilizantes/proveedor/select"]
    B --> C{"¿Hay proveedores<br/>disponibles?"}
    C -->|"No"| D["Muestra error<br/>'No hay proveedores disponibles'"]
    C -->|"Sí"| E["Usuario elige proveedor"]

    E --> F["Consulta terminales<br/>GET fertilizantes/proveedor/terminales"]
    F --> G{"¿Hay terminales?"}
    G -->|"No"| H["Deshabilita siguiente paso"]
    G -->|"Sí"| I["Usuario elige terminal"]

    I --> J["Consulta stock<br/>GET fertilizantes/proveedor/mi-reserva-stock"]
    J --> K{"¿Hay stock?"}
    K -->|"No"| L["Alerta: sin stock"]
    K -->|"Sí"| M["Usuario elige stock"]

    M --> N{"¿Contrata?"}
    N -->|"Sí (1)"| O["Flag contrata = 1"]
    N -->|"No (0)"| P["Flag contrata = 0"]
    O --> Q["POST fertilizantes/reserva/derivar"]
    P --> Q

    Q --> R{"¿Éxito?"}
    R -->|"Sí"| S["Cierra dialog<br/>Refresca lista de reservas"]
    R -->|"No"| T["Muestra error del backend"]
```

---

## Ciclo de vida de la reserva derivada

```mermaid
stateDiagram-v2
    [*] --> Activa: Reserva creada para proveedor A

    Activa --> Seleccionada: Usuario selecciona para derivar
    Seleccionada --> ProveedorElegido: Elige proveedor B
    ProveedorElegido --> TerminalElegida: Elige terminal
    TerminalElegida --> StockElegido: Elige stock
    StockElegido --> Derivada: POST derivar OK

    Derivada --> ActivaProvB: Reserva activa en proveedor B

    ActivaProvB --> [*]
```

---

## Endpoints involucrados

| Paso | Verbo | Ruta | Servicio | Propósito |
|---|---|---|---|---|
| 1 | GET | `fertilizantes/proveedor/select?id_reserva={id}` | FertilizantesService | Proveedores destino posibles |
| 2 | GET | `fertilizantes/proveedor/terminales?id_proveedor={id}` | FertilizantesService | Terminales del proveedor |
| 3 | GET | `fertilizantes/proveedor/mi-reserva-stock?id={id}` | FertilizantesService | Stock disponible en terminal |
| 4 | POST | `fertilizantes/reserva/derivar` | ReservasService | Ejecuta la derivación |

### Endpoints de soporte (consulta de reservas)

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `seguimiento/filtrar-reservas` | Filtrar reservas para selección |
| GET | `fertilizantes/reserva/by-pedido?id_pedido={id}` | Reservas por pedido |
| GET | `seguimiento/detalle-reservas` | Detalle de reservas |
| GET | `seguimiento/ver-cupo-proveedor?id_reserva={id}` | Ver cupo del proveedor |
| GET | `seguimiento/ver-cupo-cliente?id_reserva={id}` | Ver cupo del cliente |
| GET | `fertilizantes/reserva/proveedor` | Reservas del proveedor |

---

## Payload de derivación

```json
{
  "reservas": [101, 102, 103],
  "id_proveedor": 42,
  "id_origen": 7,
  "id_stock": 15,
  "contrata": 1
}
```

| Campo | Tipo | Descripción |
|---|---|---|
| `reservas` | `number[]` | IDs de reservas a derivar |
| `id_proveedor` | `number` | Proveedor destino |
| `id_origen` | `number` | Terminal de origen |
| `id_stock` | `number` | Stock seleccionado |
| `contrata` | `0 \| 1` | Si incluye contrata |

---

## Ubicación del componente

> [!info] Componente compartido
> `DerivarReservaComponent` está en `src/app/shared/components/derivar-reserva/` — forma parte de SharedModule, no del FertilizanteModule. Esto permite reutilizarlo desde diferentes módulos que manejan reservas.

---

## Relación con otros flujos

- **[[flujo-cupo]]**: Las reservas derivadas pueden estar vinculadas a cupos
- **[[flujo-pedido]]**: Las reservas pueden consultarse por pedido (`by-pedido`)

---

## Riesgos

| # | Sev. | Hallazgo |
|---|---|---|
| 1 | 🟡 | **Componente en SharedModule**: Incrementa el acoplamiento de SharedModule. Debería ser un sub-módulo del módulo Fertilizante |
| 2 | 🟡 | **Sin validación visible de cantidad**: No se verifica si el stock del destino es suficiente para las reservas derivadas (posible validación en backend) |
| 3 | 🟡 | **`contrata` como 0/1**: Flag numérico en lugar de booleano — patrón legacy |

---

## Referencias

- [[_indice-flujos]] — Índice de flujos
- [[modulo-fertilizante]] — Módulo fertilizante
- [[fertilizantes-endpoints]] — Endpoints de fertilizantes
- [[cross-module-dependencies]] — Dependencias SharedModule
