# Deuda Técnica

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21

---

## Resumen

| ID | Tipo | Severidad | Área | Estado |
|----|------|:---------:|------|:------:|
| DT-01 | Bug funcional | 🔴 Crítico | `processor.ts` | Abierto |
| DT-02 | Time bomb | 🔴 Crítico | `rt.ts` | Abierto |
| DT-03 | Limitación de negocio | 🔴 Alta | `rt.ts` | Abierto |
| DT-04 | Bug de build | 🟡 Media | `tsconfig.paths.json` | Abierto |
| DT-05 | Dead queue | 🟡 Media | `config/queues.ts`, `module.ts` | Abierto |
| DT-06 | Dead code | 🟢 Baja | `common/` | Abierto |
| DT-07 | Dep innecesaria | 🟢 Baja | `package.json` | Abierto |
| DT-08 | MySQL en compose | 🟢 Baja | `docker-compose.yml` | Abierto |

---

## DT-01 — `console.log` reemplazando persistencia de datos

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🔴 Crítico |
| **Archivo** | `src/modules/email/processor.ts` |
| **Línea aproximada** | ~95 |

**Descripción:** Al finalizar el procesamiento de cada PDF, el resultado (`ITransferencia`) se imprime con `console.log(data, name)` y se descarta. El worker no escribe en ninguna base de datos, no publica en ninguna cola downstream, no llama a ninguna API.

**Impacto:** El worker es funcionalmente inútil en producción. Procesa los PDFs correctamente pero tira los resultados.

**Esfuerzo de corrección:** Medio — requiere definir el destino del dato (API call, nueva cola Bull, módulo de BD) y agregar el cliente correspondiente.

---

## DT-02 — Cosechas hardcodeadas con fecha de vencimiento conocida

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🔴 Crítico |
| **Archivo** | `src/modules/email/functions/rt.ts` |

**Descripción:** El array de cosechas válidas está hardcodeado como `['2324', '2425', '2526', '2627', '2728']`. La campaña `2829` (año ~2028) no está incluida, lo que causará que **todos** los certificados de esa campaña fallen validación sin explicación clara.

**Impacto:** Fallo total del sistema en producción a partir de la campaña 2028-2029, requiriendo un deploy de emergencia.

**Esfuerzo de corrección:** Bajo — 3 opciones:
1. Cambiar a validación por patrón: `cosecha` debe ser 4 dígitos con formato `AABB` donde `BB = AA + 1`
2. Externalizar a una variable de entorno o configuración
3. Cargar desde BD en arranque

---

## DT-03 — Catálogo de granos incompleto

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🔴 Alta |
| **Archivo** | `src/modules/email/functions/rt.ts` |

**Descripción:** Solo 2 granos reconocidos: TRIGO PAN (id:1) y SOJA (id:2). Cualquier certificado de maíz, girasol, sorgo, cebada, etc. fallará con error de validación "grano no reconocido".

**Impacto:** El sistema no puede procesar el ~70% de los cultivos cubiertos por SENASA en Argentina.

**Esfuerzo de corrección:** Medio — requiere:
1. Externalizar el catálogo a BD o configuración
2. Agregar los IDs del sistema Muvin para cada cultivo
3. Considerar si el matching debe ser exacto o por similitud (para variaciones de nombre en el PDF)

---

## DT-04 — Path alias `@contracts` apunta a directorio inexistente

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟡 Media |
| **Archivo** | `tsconfig.paths.json` |

**Descripción:** El alias `@contracts` está definido apuntando a `src/contracts/_index`, pero el directorio `src/contracts/` no existe en el repositorio.

```json
{
  "@contracts": ["src/contracts/_index"]  // directorio inexistente
}
```

**Impacto:** Cualquier archivo que intente `import { X } from '@contracts'` fallará en compilación. Actualmente nadie importa desde `@contracts`, pero el alias incorrecto puede confundir a desarrolladores y a herramientas de análisis estático.

**Esfuerzo de corrección:** Muy bajo — eliminar el alias del `tsconfig.paths.json` o crear el directorio con su `_index.ts`.

---

## DT-05 — Cola `internal` declarada sin procesador

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟡 Media |
| **Archivos** | `src/config/queues.ts`, `src/module.ts`, `src/common/interfaces/jobs/internal/notification.ts` |

**Descripción:** La cola `internal` (proceso `internal.notification`) está declarada en `QUEUES`, tiene su interface `IJobInternalNotification`, pero no hay procesador registrado en `AppModule` ni implementado en ningún módulo. Además, la cola no está registrada en `BullModule.registerQueue()`.

**Impacto:** Cualquier job publicado en `internal.notification` quedará encolado en Redis indefinidamente (nunca procesado), causando acumulación en el broker.

**Esfuerzo de corrección:** Bajo si se elimina la cola, Alto si se implementa el procesador.

---

## DT-06 — Dead code en `src/common/`

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟢 Baja |
| **Archivo** | `src/common/cmd/`, `src/common/functions/` (parcialmente) |

**Descripción:** ~65% del código en `src/common/` no es usado por este worker. Los `CMDS` (patrones de mensajes de microservicio), la función `identity.ts`, y varias interfaces están copiados del API principal de Muvin pero no tienen uso aquí.

**Impacto:** Aumenta el tamaño del bundle, confunde a nuevos desarrolladores, y puede causar dependencias transitivas innecesarias.

**Esfuerzo de corrección:** Bajo — eliminar los archivos no usados. Ver [[functional-classification]] para el inventario completo.

---

## DT-07 — `@nestjs/platform-express` innecesario en producción

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟢 Baja |
| **Archivo** | `package.json` |

**Descripción:** `@nestjs/platform-express` (y Express.js con todas sus dependencias) está en `dependencies`. El worker no expone ningún servidor HTTP — `app.listen()` no se llama. Esta dependencia solo aumenta la superficie de ataque y el tamaño de imagen Docker.

**Esfuerzo de corrección:** Muy bajo — mover a `devDependencies` o remover si no es requerido por NestFactory internamente.

---

## DT-08 — MySQL en `docker-compose.yml` para un worker sin BD

| Atributo | Valor |
|---------|-------|
| **Severidad** | 🟢 Baja |
| **Archivo** | `docker/docker-compose.yml` |

**Descripción:** El `docker-compose.yml` define un servicio MySQL 8.0, pero el worker no tiene ORM, ningún módulo de base de datos, y ningún uso de MySQL en el código. El servicio del worker principal está comentado. El compose parece ser un remanente del template del API principal.

**Esfuerzo de corrección:** Bajo — reemplazar con un compose que incluya solo Redis (que es lo que el worker realmente necesita).
