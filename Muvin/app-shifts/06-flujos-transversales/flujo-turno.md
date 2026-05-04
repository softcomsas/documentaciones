# Flujo de turno de puerto — app-shifts

## Diagrama completo

```
HomePage
  └── menú "Turno" → /turno

TurnoPage (onInit)
  └── obtenerTurnoReservado()
        POST turno-puerto/actual
        ├── data != null → muestra turno activo
        │     ├── fecha, hora, ventanilla, puerto, producto
        │     └── botón "Reprogramar"
        └── data == null → muestra formulario de reserva

─── RESERVAR NUEVO TURNO ───────────────────────────────────

TurnoBloc (formulario reactivo)
  changePuerto(id)      → puertoStream → validarRequerido
  changeProducto(id)    → productoStream → validarRequerido
  changeFecha(fecha)    → fechaStream → validarRequerido
  changeInicio(hora)    → inicioStream → validarRequerido
  changeFin(hora)       → finStream → validarRequerido
  changeHorario(idHor)  → horarioStream → validarRequerido
  changeTextoCartaPorte → textoCartaPorteStream
  changeTextoCtg(ctg)   → ctgStream

  formValidStream = combineLatest5(puerto,producto,fecha,inicio,fin)
    → true → habilita botón "Buscar ventanillas"

  → obtenerTurnos({puerto, producto, fecha, inicio, fin})
        POST turno-puerto/select
        ├── 200 → lista de ventanillas disponibles
        ├── 422 → "Por favor intente en 30 minutos."
        └── error → mensaje genérico

  → usuario selecciona ventanilla → changeHorario(id)
  → usuario escanea CTG con barcode scanner → changeTextoCtg(ctg)
  → usuario ingresa número carta de porte → changeTextoCartaPorte(num)

  → confirmarTurno()
        POST turno-puerto {puerto, producto, fecha, horario, textoCartaPorte, textoCtg, ...}
        ├── ok: true → Preferences.turneada = id_turno
        │             → SweetAlert "Turno confirmado"
        │             → vuelve a HomePage o refresca
        └── ok: false → muestra errores del backend

─── REPROGRAMAR TURNO EXISTENTE ────────────────────────────

TurnoPage → botón "Reprogramar"
  → obtenerTurnos(nuevosDatos)
        POST turno-puerto/select → nuevas ventanillas

  → usuario selecciona nueva ventanilla
  → reprogramarTurno(idTurno, nuevosDatos)
        PUT turno-puerto/{id}
        ├── ok: true → SweetAlert "Turno reprogramado"
        └── ok: false → muestra error
```

## Relación con el viaje activo

El campo `Preferences.turneada` actúa como indicador de que el viaje activo ya tiene turno asignado. Esto se muestra en `ViajePage` para indicar al chofer que puede dirigirse al puerto.

---

Ver también: [[turnos]] · [[calada]] · [[viaje]] · [[endpoints-rest]]
