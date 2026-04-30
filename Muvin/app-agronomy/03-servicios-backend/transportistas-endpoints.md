# Endpoints de Transportistas

> **Archivo fuente:** `src/app/pages/transportistas/services/camion.service.ts`, `chofer.service.ts`
> **Base URL:** `environment.apiURL`

---

## `GET camion`

**Propósito:** Listado paginado de camiones con filtro por patente.
**Autenticación:** Token requerido
**Consumido por:** [[modulo-transportistas]]

### Parámetros

| Nombre | Ubicación | Tipo | Descripción |
|--------|-----------|------|-------------|
| Search[patente] | query | string | Filtro por patente parcial |
| page | query | int | Número de página |

### Respuesta

```json
{ "data": [...camiones], "_meta": { "pageCount": N } }
```

---

## `GET camion/patente-existe`

**Propósito:** Verificar si una patente ya está registrada en el sistema.
**Consumido por:** [[modulo-transportistas]] (validación en formulario de alta)

### Parámetros

| Nombre | Ubicación | Tipo | Descripción |
|--------|-----------|------|-------------|
| patente | query | string | Patente a verificar |

---

## `POST chofer/carga-masiva`

**Propósito:** Alta masiva de choferes.
**Autenticación:** Token requerido
**Consumido por:** [[modulo-transportistas]]

### Payload

```json
[ { "⚠️ Pendiente de verificar": "estructura de dataChofer[]" } ]
```

### Riesgos

- ⚠️ `console.log(chofer)` presente en el servicio antes del POST. Expone datos en consola en producción. 🔒

---

> [!warning] Funcionalidad limitada documentada
> `CamionService` tiene ~6 métodos activos de ~12 existentes. La mitad están comentados, indicando refactor incompleto. Ver [[modulo-transportistas]] para detalle.
