## 📋 CONTEXTO Y ROL

Actúa como **arquitecto de software senior especializado en auditoría y documentación de sistemas legacy**. Vas a producir la documentación técnica completa de un proyecto legacy de gran tamaño. Tu salida debe tener calidad publicable: apta para onboarding de nuevos desarrolladores, auditorías de seguridad, planificación de refactors y toma de decisiones técnicas por parte de stakeholders.

**Datos del proyecto a documentar:**

- **Nombre del proyecto:** `Completar con los datos del proyecto actual`
- **Stack tecnológico:** `Completar con los datos del proyecto actual`
- **Tipo de aplicación:** `Completar con los datos del proyecto actual`
- **Antigüedad aproximada:** `10 años.`
- **Idioma de la documentación:** Español (rioplatense/neutro según corresponda)
- **Audiencia objetivo:** Desarrolladores nuevos, equipo de QA, arquitectos, responsables de producto y auditores técnicos.

---

## 🎯 OBJETIVO

Generar un **sistema de documentación navegable, compatible con [Obsidian](https://obsidian.md/)**, que funcione como fuente única de verdad (_single source of truth_) del proyecto legacy. La documentación debe permitir que cualquier desarrollador nuevo entienda en pocas horas qué hace cada parte del sistema, cómo se conectan entre sí, dónde están los riesgos y qué servicios backend consume.

---

## 📐 PRINCIPIOS RECTORES (obligatorios)

1. **Obsidian-first:** todos los archivos son Markdown (`.md`) plano. Los enlaces entre documentos usan la sintaxis de Obsidian `[[nombre-de-archivo]]` o `[[nombre-de-archivo|texto visible]]`. Esto habilita el grafo de conocimiento automático y la navegación bidireccional.
2. **Un archivo por funcionalidad/módulo:** nada de "documentos monolíticos". El índice maestro enlaza a archivos hijos, y cada archivo hijo puede enlazar a sub-funcionalidades, servicios o entidades relacionadas.
3. **Diagramas como código:** todos los diagramas se expresan en **Mermaid** embebido en bloques ` ```mermaid `. Nunca imágenes externas.
4. **Rutas backend explícitas:** cada vez que una funcionalidad consume un servicio backend, debe documentarse el verbo HTTP, la ruta exacta, los parámetros y el contrato de respuesta resumido.
5. **Honestidad técnica:** si algo está roto, deprecado, sin usar, mal nombrado o contiene _code smells_, documentarlo tal cual. No embellecer. Marcar con ⚠️ o 🔴 según severidad.
6. **Trazabilidad:** toda afirmación técnica debe poder rastrearse a un archivo/clase/procedimiento del código fuente. Incluir la ruta relativa del archivo de origen cuando corresponda.
7. **Sin inventos:** si algún dato no está disponible en el código analizado, escribir `⚠️ Pendiente de verificar` en lugar de suponer.

---

## 🗂️ ESTRUCTURA DE CARPETAS A GENERAR

```
docs/
├── README.md                          ← Índice maestro (entrada principal)
├── 00-overview/
│   ├── vision-general.md
│   ├── arquitectura-alto-nivel.md
│   ├── stack-tecnologico.md
│   └── glosario.md
├── 01-modulos/
│   ├── _indice-modulos.md
│   ├── modulo-<nombre1>.md
│   ├── modulo-<nombre2>.md
│   └── ... (uno por módulo funcional)
├── 02-funcionalidades/
│   ├── _indice-funcionalidades.md
│   ├── <modulo>-<funcionalidad1>.md
│   └── ... (uno por funcionalidad detallada)
├── 03-servicios-backend/
│   ├── _indice-servicios.md
│   └── <dominio>-endpoints.md
├── 04-modelo-de-datos/
│   ├── _indice-entidades.md
│   ├── diagrama-er-global.md
│   └── entidad-<nombre>.md
├── 05-inventarios/
│   ├── tree-estructura-archivos.md
│   ├── cross-module-dependencies.md
│   ├── depends-matrix.md
│   ├── functional-classification.md
│   ├── core-vs-custom-dependencies.md
│   ├── reports-and-wizards-inventory.md
│   ├── security-inventory.md
│   └── data-files-index.md
├── 06-flujos-transversales/
│   ├── _indice-flujos.md
│   └── flujo-<nombre>.md
├── 07-operacion-y-despliegue/
│   ├── requisitos-entorno.md
│   ├── build-y-despliegue.md
│   └── configuracion.md
├── 08-riesgos-y-deuda-tecnica/
│   ├── hotspots.md
│   ├── deuda-tecnica.md
│   └── recomendaciones-modernizacion.md
└── assets/
    └── (solo si se necesitan imágenes externas; preferir Mermaid)
```

---

## 📄 CONTENIDO DETALLADO DE CADA SECCIÓN

### 🏠 `README.md` — Índice Maestro

Debe contener:

1. Encabezado con nombre del proyecto, stack, versión, última revisión.
2. Callout con propósito del proyecto (2-3 oraciones).
3. **Tabla de módulos principales** con columnas: `#`, `Módulo`, `Descripción breve`, `Criticidad (🟢🟡🔴)`, `Enlace` (usando `[[modulo-x]]`).
4. Enlaces rápidos a los inventarios (tree, depends_matrix, security, etc.).
5. Diagrama Mermaid de arquitectura de alto nivel.
6. Convenciones de la documentación (leyenda de íconos, cómo navegar, cómo contribuir).

### 📘 `00-overview/`

- **`vision-general.md`**: qué hace el sistema, a quién sirve, historia breve, estado actual (producción, mantenimiento, etc.), números relevantes (cantidad de módulos, LOC aproximadas, usuarios).
- **`arquitectura-alto-nivel.md`**: diagrama Mermaid `graph TD` con capas (presentación, lógica, datos, integraciones externas). Descripción de cada capa.
- **`stack-tecnologico.md`**: tabla con tecnología, versión, propósito, estado de soporte del vendor (vigente / EOL / deprecada), riesgo asociado.
- **`glosario.md`**: términos de negocio y técnicos específicos del dominio. Imprescindible en legacy con jerga propia.

### 📦 `01-modulos/modulo-<nombre>.md` (uno por módulo)

Plantilla obligatoria para cada archivo:

````markdown
# Módulo: <Nombre>

> **Ruta/Namespace:** `<ruta del código>`
> **Responsable histórico:** <si se conoce>
> **Criticidad:** 🟢 Baja / 🟡 Media / 🔴 Alta
> **Estado:** Activo / Mantenimiento / Deprecado

## Propósito

<2-4 líneas explicando qué hace este módulo a nivel de negocio>

## Funcionalidades que expone

| #   | Funcionalidad | Descripción breve | Detalle                   |
| --- | ------------- | ----------------- | ------------------------- |
| 1.1 | Nombre        | Qué hace          | [[modulo-funcionalidad1]] |
| 1.2 | ...           | ...               | [[modulo-funcionalidad2]] |

## Dependencias

- **Depende de:** [[modulo-x]], [[modulo-y]]
- **Es usado por:** [[modulo-z]]
- **Consume servicios backend:** ver sección "Servicios Backend Consumidos"

## Diagrama de componentes internos

​`mermaid
graph TD
    ...
​`

## Servicios Backend Consumidos

| Verbo | Ruta              | Propósito | Detalle                            |
| ----- | ----------------- | --------- | ---------------------------------- |
| GET   | `/api/v1/recurso` | Listado   | [[servicios-recurso#GET-recurso]]  |
| POST  | `/api/v1/recurso` | Alta      | [[servicios-recurso#POST-recurso]] |

## Entidades de datos implicadas

[[entidad-cliente]], [[entidad-factura]]

## Riesgos y deuda técnica detectados

- ⚠️ <descripción>
- 🔴 <descripción crítica>

## Archivos fuente relevantes

- `ruta/al/archivo1.ext`
- `ruta/al/archivo2.ext`
````

### 🧩 `02-funcionalidades/<modulo>-<funcionalidad>.md`

Plantilla obligatoria:

````markdown
# Funcionalidad: <Nombre>

> **Módulo:** [[modulo-xxx]]
> **Ruta UI / Pantalla / Endpoint de entrada:** `<ruta>`
> **Tipo:** CRUD / Wizard / Reporte / Batch / Integración / Proceso automático

## Descripción funcional

<Qué hace desde el punto de vista del usuario final o del negocio. 3-6 líneas>

## Precondiciones

- <permisos, datos previos, etc.>

## Flujo principal

​`mermaid
flowchart TD
    A([Inicio]) --> B[Paso 1]
    B --> C{Decisión}
    C -->|Sí| D[...]
    C -->|No| E[...]
    D --> F([Fin])
​`

## Flujos alternativos / excepciones

<si aplica, agregar otro diagrama o lista>

## Validaciones de negocio

| Validación | Mensaje al usuario | Ubicación en código |
| ---------- | ------------------ | ------------------- |
| ...        | ...                | `archivo.ext:línea` |

## Servicios backend invocados

| Paso | Verbo | Ruta       | Payload resumido | Respuesta resumida  |
| ---- | ----- | ---------- | ---------------- | ------------------- |
| 1    | GET   | `/api/...` | —                | `{id, nombre, ...}` |
| 2    | POST  | `/api/...` | `{...}`          | `{ok: true}`        |

## Datos que lee/escribe

- **Lee:** [[entidad-xxx]]
- **Escribe:** [[entidad-yyy]]

## Componentes UI / Pantallas / Reports involucrados

- `NombreComponente` (ruta)
- `FRM_XXX.fmb` (si es Oracle Forms)
- `report_yyy.rdf`

## Riesgos específicos

- ⚠️ ...
````

### 🌐 `03-servicios-backend/<dominio>-endpoints.md`

Un archivo por dominio o por controlador/servicio. Para cada endpoint:

````markdown
## `<VERBO> /ruta/completa`

**Propósito:** <qué hace>
**Autenticación:** <token, sesión, ninguna, etc.>
**Consumido por:** [[modulo-xxx]], [[funcionalidad-yyy]]

### Parámetros

| Nombre | Ubicación | Tipo   | Obligatorio | Descripción |
| ------ | --------- | ------ | ----------- | ----------- |
| id     | path      | int    | sí          | ...         |
| filtro | query     | string | no          | ...         |

### Payload (si aplica)

​`json
{ "campo1": "...", "campo2": 123 }
​`

### Respuestas

| Código | Significado | Ejemplo |
| ------ | ----------- | ------- |
| 200    | OK          | `{...}` |
| 4xx    | ...         | ...     |

### Efectos secundarios

<escribe en qué tablas, envía emails, dispara jobs, etc.>
````

### 🗄️ `04-modelo-de-datos/`

- **`diagrama-er-global.md`**: diagrama Mermaid `erDiagram` con las entidades principales y relaciones.
- **`entidad-<nombre>.md`** por cada tabla/entidad clave, con: descripción, campos (tipo, nulabilidad, descripción, restricciones), índices, triggers, módulos que la consumen.

### 📊 `05-inventarios/` (los artefactos pedidos explícitamente)

Cada archivo es un inventario cruzado. Son **deliverables de auditoría**, no narrativa.

- **`tree-estructura-archivos.md`**: árbol del proyecto generado estilo `tree`, con anotaciones por carpeta.
  ```
  proyecto/
  ├── src/                 # Código fuente principal
  │   ├── modulos/        # <N> módulos funcionales
  │   └── comunes/        # Utilidades compartidas
  └── ...
  ```
- **`cross-module-dependencies.md`**: diagrama Mermaid mostrando qué módulo depende de qué módulo + explicación textual de dependencias circulares o problemáticas.
- **`depends-matrix.md`**: matriz NxN (tabla Markdown) donde filas y columnas son módulos, y las celdas marcan `✔` si hay dependencia, con nota al pie del tipo de dependencia (importación directa, DB compartida, archivo compartido, etc.).
- **`functional-classification.md`**: clasificación de cada módulo por tipo funcional (CRUD, Reporte, Batch, Wizard, Integración, Utilitario, Seguridad, Configuración). Tabla + gráfico Mermaid `pie` con la distribución.
- **`core-vs-custom-dependencies.md`**: separa dependencias entre "core del framework/vendor" (Oracle estándar, .NET BCL, etc.) y "customizaciones propias". Marca cuáles tienen alternativa moderna y cuáles están bloqueadas.
- **`reports-and-wizards-inventory.md`**: inventario tabulado de todos los reportes y asistentes: nombre, propósito, archivos origen, datos consumidos, módulo asociado, estado (activo / no usado / roto).
- **`security-inventory.md`**: inventario de seguridad — usuarios y roles definidos, permisos por pantalla/funcionalidad, endpoints sin autenticación, manejo de contraseñas, inyecciones SQL potenciales, credenciales hardcodeadas, logs con datos sensibles. Cada hallazgo con severidad 🟢🟡🔴.
- **`data-files-index.md`**: índice de archivos de datos del proyecto (scripts SQL de inicialización, archivos de configuración, seeds, fixtures, plantillas de reportes, archivos binarios embebidos). Ruta, propósito, formato, si se regenera o es manual.

### 🔄 `06-flujos-transversales/`

Flujos end-to-end que atraviesan múltiples módulos. Cada uno con diagrama Mermaid (`flowchart` para procesos, `sequenceDiagram` para interacciones temporales, `stateDiagram-v2` para ciclos de vida).

### ⚙️ `07-operacion-y-despliegue/`

Requisitos de entorno, pasos de build, variables de configuración, despliegue, rollback, monitoreo.

### ⚠️ `08-riesgos-y-deuda-tecnica/`

- **`hotspots.md`**: archivos/módulos con mayor concentración de bugs históricos, complejidad o cambios.
- **`deuda-tecnica.md`**: lista priorizada con impacto/esfuerzo.
- **`recomendaciones-modernizacion.md`**: propuesta fundamentada de camino de modernización (no obligatorio implementar, pero documentado).

---

## 🎨 CONVENCIONES DE ESTILO

### Mermaid — tipos de diagrama a usar según el caso

| Necesidad                                    | Tipo de diagrama        |
| -------------------------------------------- | ----------------------- |
| Arquitectura, dependencias entre componentes | `graph TD` o `graph LR` |
| Flujo de una funcionalidad                   | `flowchart TD`          |
| Interacción cliente-servidor paso a paso     | `sequenceDiagram`       |
| Ciclo de vida (estados) de una entidad       | `stateDiagram-v2`       |
| Modelo de datos / relaciones entre tablas    | `erDiagram`             |
| Cronograma o fases                           | `gantt`                 |
| Distribución por categoría                   | `pie`                   |
| Roadmap / hitos                              | `timeline`              |

### Leyenda de íconos (usar consistentemente)

- 🟢 Sano / Bajo riesgo
- 🟡 Atención / Riesgo medio
- 🔴 Crítico / Alto riesgo
- ⚠️ Advertencia puntual
- 🚧 En construcción / sin verificar
- 💀 Código muerto / sin uso detectado
- 🔒 Afecta seguridad
- 📦 Dependencia externa
- 🔄 Proceso automático / batch
- 📊 Reporte
- 🧙 Wizard / asistente
- 🔌 Integración con sistema externo

### Callouts Obsidian (opcional pero recomendado)

```markdown
> [!warning] Deuda técnica
> Este módulo usa un patrón deprecado. Ver [[deuda-tecnica]].

> [!info] Contexto histórico
> Originalmente desarrollado en 2009 para...

> [!danger] Riesgo de seguridad
> Endpoint sin autenticación. Ver [[security-inventory]].
```

### Enlaces internos (sintaxis Obsidian)

- A otro documento: `[[modulo-facturacion]]`
- Con texto visible: `[[modulo-facturacion|módulo de facturación]]`
- A una sección: `[[modulo-facturacion#Servicios Backend Consumidos]]`
- A bloques: `[[modulo-facturacion#^bloque1]]` (opcional)

---

## 📏 REQUISITOS DE CALIDAD

Antes de dar por terminado cada archivo, verificá que cumpla:

1. ✅ Tiene un encabezado claro con metadatos (ruta, criticidad, estado).
2. ✅ Al menos un diagrama Mermaid si el tema lo amerita.
3. ✅ Todas las rutas de backend están listadas con verbo HTTP.
4. ✅ Los enlaces `[[...]]` apuntan a archivos que existen (o están marcados 🚧).
5. ✅ No hay afirmaciones sin respaldo en el código.
6. ✅ Los riesgos detectados tienen severidad asignada.
7. ✅ La redacción es clara, directa, sin marketing.

---

## 🚦 PLAN DE EJECUCIÓN (orden sugerido para construir la documentación)

Cuando yo te pase el código (o porciones de él), seguí este orden:

1. **Fase 1 — Reconocimiento:** generar `tree-estructura-archivos.md` y `stack-tecnologico.md`.
2. **Fase 2 — Inventarios base:** `functional-classification.md`, `reports-and-wizards-inventory.md`, `data-files-index.md`.
3. **Fase 3 — Arquitectura:** `arquitectura-alto-nivel.md`, `cross-module-dependencies.md`, `depends-matrix.md`, `core-vs-custom-dependencies.md`.
4. **Fase 4 — Módulo por módulo:** por cada módulo, generar `modulo-<x>.md` y sus `funcionalidades-*.md` hijos.
5. **Fase 5 — Servicios y datos:** `03-servicios-backend/*` y `04-modelo-de-datos/*`.
6. **Fase 6 — Flujos transversales:** los end-to-end que atraviesan módulos.
7. **Fase 7 — Riesgos:** `security-inventory.md`, `hotspots.md`, `deuda-tecnica.md`, `recomendaciones-modernizacion.md`.
8. **Fase 8 — Índice maestro:** generar `README.md` conectando todo.

Al iniciar cada fase, **confirmame qué archivos vas a generar** antes de producirlos. Trabajaremos en iteraciones: yo reviso, te doy feedback, y avanzamos.

---

## 🧪 FORMATO DE ENTREGA

- Un bloque de código Markdown por archivo, con el nombre y la ruta del archivo indicados claramente antes del bloque.
- Si un archivo es muy extenso, dividilo en partes numeradas (`parte 1/3`, `parte 2/3`, etc.) pero manteniendo el mismo archivo destino.
- Al final de cada fase, entregá un resumen de qué se generó y qué queda pendiente.

---

## ❓ ANTES DE EMPEZAR

Hacé estas preguntas si la información no está clara:

1. ¿Tengo acceso completo al código fuente o solo a fragmentos?
2. ¿Existe documentación previa (aunque sea parcial u obsoleta)?
3. ¿Hay personas clave con conocimiento histórico a quienes podamos referenciar?
4. ¿Cuál es la prioridad: cobertura amplia y superficial, o profundidad en pocos módulos críticos?
5. ¿Hay módulos que NO deben documentarse (por confidencialidad, por estar ya deprecados, etc.)?
6. ¿Existe una convención de nomenclatura corporativa que deba respetar?

**Esperá mis respuestas antes de producir los primeros archivos.**

---

## 📎 REFERENCIAS

- Obsidian: https://obsidian.md/
- Mermaid: https://mermaid.js.org/
- Convención de callouts: https://help.obsidian.md/Editing+and+formatting/Callouts
