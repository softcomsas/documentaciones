# Documentación — muvin-ms-logs

> Fuente única de verdad del microservicio de logs y trazabilidad del ecosistema BCR-Muvin.
> Compatible con Obsidian — todos los links son `[[wiki-links]]`.

## Leyenda de iconos

| Ícono | Significado |
|-------|-------------|
| 🔴 | Bug crítico o riesgo alto |
| ⚠️ | Advertencia / deuda técnica |
| 🟡 | Estado parcial o mejorable |
| 🟢 | Estado correcto |
| 💀 | Dead code / funcionalidad sin uso |
| 🚧 | Pendiente de verificar |
| 🔒 | Consideración de seguridad |

---

## Estructura de la documentación

### 00 — Overview

| Archivo | Descripción |
|---------|-------------|
| [[vision-general]] | Qué es el servicio, posición en el ecosistema, estado actual |
| [[stack-tecnologico]] | Stack completo con versiones y análisis de riesgo |
| [[arquitectura-alto-nivel]] | Diagramas de capas, flujos y posición en BCR-Muvin |
| [[glosario]] | Términos de negocio agrícola y técnicos del MS |

### 01 — Módulos

| Archivo | Descripción |
|---------|-------------|
| [[_indice-modulos]] | Tabla de todos los módulos |
| [[modulo-microservices]] | Módulo de trazas GraphQL y eventos |
| [[modulo-legacy]] | Módulo de logs de sistemas legados |

### 02 — Funcionalidades

| Archivo | Pattern TCP | Descripción |
|---------|-------------|-------------|
| [[_indice-funcionalidades]] | — | Índice completo |
| [[microservices-trace-create]] | `logs.trace.create` | Inicia una traza GraphQL |
| [[microservices-trace-update]] | `logs.trace.update` | Cierra una traza con respuesta |
| [[microservices-event-create]] | `logs.event.create` | Registra llamada a un MS |
| [[microservices-event-update]] | `logs.event.update` ⚠️ BUG | Debería cerrar el evento — nunca se ejecuta |
| [[legacy-create]] | `logs.legacy.create` | Registra inicio de request legado |
| [[legacy-update]] | `logs.legacy.update` | Cierra registro legado con respuesta |
| [[legacy-search-id]] | `logs.legacy.search.id` | Busca registro por ID (retorna datos) |
| [[legacy-search-user]] | `logs.legacy.search.user` | Busca registros por usuario (paginado) |
| [[legacy-search-terms]] | `logs.legacy.search.terms` | Búsqueda por términos de negocio |

### 03 — Servicios Backend

| Archivo | Descripción |
|---------|-------------|
| [[_indice-servicios]] | Resumen de los 9 patterns TCP |
| [[message-patterns-endpoints]] | Referencia completa con payloads y respuestas |

### 04 — Modelo de Datos

| Archivo | Descripción |
|---------|-------------|
| [[_indice-entidades]] | ER global + índice de entidades |
| [[entidad-traces]] | Tabla `traces` — trazas GraphQL |
| [[entidad-events]] | Tabla `events` — eventos de MS (🔴 datos incompletos por bug) |
| [[entidad-users]] | Tabla `users` — mirror de usuarios |
| [[entidad-legacy]] | Tablas `legacy_panel`, `legacy_descargas`, `legacy_actions`, `legacy_user_actions` |

### 05 — Inventarios

| Archivo | Descripción |
|---------|-------------|
| [[tree-estructura-archivos]] | Árbol anotado del directorio |
| [[functional-classification]] | Clasificación funcional de archivos |
| [[data-files-index]] | Variables de entorno y archivos de configuración |
| [[cross-module-dependencies]] | Grafo de dependencias entre módulos |
| [[depends-matrix]] | Matriz NxN de dependencias |
| [[core-vs-custom-dependencies]] | Dependencias vendor vs. custom |
| [[reports-and-wizards-inventory]] | Operaciones de lectura disponibles |
| [[security-inventory]] | Superficies de ataque y estado de seguridad |

### 06 — Flujos Transversales

| Archivo | Descripción |
|---------|-------------|
| [[_indice-flujos]] | Índice de flujos |
| [[flujo-tracing-graphql]] | Ciclo de vida completo de una traza GraphQL |
| [[flujo-legacy-logging]] | Ciclo de vida completo de un log legado |

### 07 — Operación y Despliegue

| Archivo | Descripción |
|---------|-------------|
| [[requisitos-entorno]] | Variables de entorno, Node.js, MySQL |
| [[build-y-despliegue]] | Scripts npm, Docker, Prisma migrations |
| [[configuracion]] | Transport, path aliases, ValidationPipe |

### 08 — Riesgos y Deuda Técnica

| Archivo | Descripción |
|---------|-------------|
| [[hotspots]] | Archivos y funciones de mayor complejidad/riesgo |
| [[deuda-tecnica]] | Registro priorizado de bugs y deuda |
| [[recomendaciones-modernizacion]] | Roadmap de mejoras corto/mediano/largo plazo |

---

## Quick start para developers

```bash
# 1. Levantar MySQL
cd docker && docker compose up -d

# 2. Configurar entorno
cp .env.example .env   # ⚠️ .env.example no existe — crear manualmente
# Ver: docs/07-operacion-y-despliegue/requisitos-entorno.md

# 3. Inicializar BD
npx prisma generate
npx prisma migrate deploy

# 4. Iniciar en desarrollo
npm run start:dev
```

## Bug crítico conocido

> [!danger] event.update nunca se ejecuta
> `src/common/cmd/constant.ts` línea ~17: `event.update: 'logs.event.create'` debe ser `'logs.event.update'`.
> **Todos los eventos en producción tienen `response`, `duration` y `finishedAt` = null.**
> Ver [[microservices-event-update]] y [[deuda-tecnica]].
