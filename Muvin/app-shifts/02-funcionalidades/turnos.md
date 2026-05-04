# Turnos de puerto — app-shifts

## Descripción

La funcionalidad de **turnos** es la característica central de `app-shifts`. Permite al chofer **reservar un horario de ingreso** a un puerto o terminal, **consultar su turno activo** y **reprogramarlo** si es necesario.

El BLoC `TurnoBloc` gestiona el estado del formulario con validación por streams.

## Campos del formulario (TurnoBloc)

| Campo | Stream | Descripción |
|-------|--------|-------------|
| `puerto` | `puertoStream` | ID o nombre del puerto destino |
| `producto` | `productoStream` | Tipo de grano/producto |
| `fecha` | `fechaStream` | Fecha del turno |
| `inicio` | `inicioStream` | Hora de inicio de la ventanilla |
| `fin` | `finStream` | Hora de fin de la ventanilla |
| `horario` | `horarioStream` | ID de la ventanilla horaria |
| `textoCartaPorte` | `textoCartaPorteStream` | Número de carta de porte |
| `textoCtg` | `ctgStream` | Código CTG (Trazabilidad de Granos) |

El formulario se habilita cuando los primeros 5 campos son válidos: `Observable.combineLatest5(puerto, producto, fecha, inicio, fin)`.

## Flujo de reserva de turno

```
TurnoPage
  → usuario completa formulario (puerto, producto, fecha, ventanilla)
    → TurnoBloc valida campos via streams
      → formValidStream: true → botón "Confirmar" activo

  → confirmar
    → MuvinProvider.obtenerTurnos({puerto, producto, fecha, inicio, fin})
      → POST turno-puerto/select
        → lista de ventanillas disponibles

    → usuario selecciona ventanilla
      → MuvinProvider.confimarTurno({...datos})
        → POST turno-puerto
          → {ok: true} → turno confirmado
            → Preferences.turneada = id_turno
```

## Consultar turno activo

```
TurnoPage (al abrir)
  → MuvinProvider.obtenerTurnoReservado({puerto, id_chofer, ...})
    → POST turno-puerto/actual
      → {ok: true, data: turnoActual} → muestra turno vigente
      → {ok: true, data: null}        → no hay turno activo
```

## Reprogramar turno

```
TurnoPage → botón "Reprogramar"
  → MuvinProvider.obtenerTurnos(...)
    → POST turno-puerto/select → nuevas ventanillas

  → usuario selecciona nueva ventanilla
    → MuvinProvider.reprogramarTurno(id, {nuevosDatos})
      → PUT turno-puerto/{id}
        → {ok: true} → turno actualizado
```

## Endpoints involucrados

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `turno-puerto/actual` | Obtiene el turno activo del chofer |
| POST | `turno-puerto/select` | Lista ventanillas disponibles (con filtros) |
| POST | `turno-puerto` | Confirma/reserva un nuevo turno |
| PUT | `turno-puerto/{id}` | Reprograma un turno existente |

## Notas

- Si el endpoint `turno-puerto/select` responde con **422**, el backend devuelve: `"Por favor intente en 30 minutos."` — indica un bloqueo temporal.
- El campo `turneada` en `Preferences` persiste el estado local del turno del viaje activo.
- En el código existe una ruta `/turno` comentada que apuntaba a `TurnoProxPage` con la nota: `"30-09 florencia, TKN#275"` — indica que hubo una alternativa de página de "próximos turnos" que fue desactivada.

---

Ver también: [[calada]] · [[viaje]] · [[endpoints-rest]] · [[flujo-turno]]
