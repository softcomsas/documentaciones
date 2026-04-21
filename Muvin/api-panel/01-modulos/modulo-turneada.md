# Módulo Turneada — Gestión de Turnos

> **Última revisión:** 2026-04-21
> **Namespace:** `turneada\`
> **Ruta:** `backend/modules/turneada/`
> **Ver también:** [[modulo-v3]], [[_indice-modulos]]

---

## Propósito

El módulo **turneada** gestiona el sistema de **turnos de descarga** en los centros/terminales. Permite organizar a los vehículos en grupos y listas de espera para la descarga, optimizando el flujo operativo de los puertos/terminales.

---

## Controladores

| Controlador | Propósito |
|-------------|-----------|
| `ListaController` | Gestión de listas de turneada (CRUD + select) |
| `GrupoController` | Gestión de grupos de turneada (CRUD + select) |
| `GrupoTransporteController` | Vinculación de grupos con medios de transporte |
| `ControlViajesController` | Control de viajes en el contexto de turneada |

---

## Endpoints de ListaController

| Acción | Método | Propósito |
|--------|--------|-----------|
| `actionIndex` | GET | Listado de listas de turneada |
| `actionView($id)` | GET | Ver detalle de una lista |
| `actionCreate` | POST | Crear nueva lista |
| `actionUpdate($id)` | PUT | Actualizar lista |
| `actionDelete($id)` | DELETE | Eliminar lista |
| `actionSelect` | GET | Lista de opciones para dropdown |

---

## Concepto de negocio

```
Centro/Terminal
└── Lista de Turneada (ej. "Turno Mañana - Maíz")
    └── Grupo de Turneada (ej. "Grupo 1 - CUIT 20xxxxxxxx")
        └── Cupos/Vehículos asignados a ese turno
```

El sistema de turneada permite que el panel operativo del terminal organice los camiones en grupos prioritarios para la descarga, evitando colas desordenadas.

---

## Integración con otros módulos

- Los cupos del módulo **v3** pueden tener un turno asociado (campo `id_turneada`)
- Las notificaciones de turno se envían a través del componente `Notificacion` (SMS/WhatsApp/Socket)

---

## Notas

> [!info] Tipos de turneada
> La tabla de configuración `tipo_turneada` permite configurar distintos modos de turneado por centro.
