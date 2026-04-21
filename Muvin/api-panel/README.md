# Muvinapp API Panel — Documentación Técnica

> **Base de conocimiento técnico** del proyecto `api-panel` de Muvinapp.
> Generado por análisis del código fuente real. Última revisión: 2026-04-21.

---

## Inicio rápido

| Quiero entender... | Ir a |
|---------------------|------|
| ¿Qué es el sistema? | [[vision-general]] |
| La arquitectura general | [[arquitectura-alto-nivel]] |
| El stack tecnológico | [[stack-tecnologico]] |
| Los términos del negocio | [[glosario]] |
| Un módulo específico | [[_indice-modulos]] |
| Los flujos principales | [[_indice-flujos]] |
| Los riesgos de seguridad | [[security-inventory]] |
| Cómo desplegar | [[despliegue-docker]] |

---

## Estructura de la documentación

```
docs/
├── 00-overview/              ← Visión general y contexto
│   ├── vision-general        ← Qué es Muvinapp, actores, alcance
│   ├── arquitectura-alto-nivel ← Diagrama de sistema completo
│   ├── stack-tecnologico     ← PHP/Yii2, dependencias, integraciones
│   └── glosario              ← Términos de negocio y siglas
│
├── 01-modulos/               ← Documentación por módulo
│   ├── _indice-modulos       ← Índice y mapa de módulos
│   ├── modulo-v3             ← V3: Cupos + Carta de Porte (CORE)
│   ├── modulo-v2             ← V2: Cupos legacy
│   ├── modulo-cupos          ← Dominio base de cupos
│   ├── modulo-viajes         ← Viajes y solicitudes
│   ├── modulo-choferes       ← Gestión de choferes y flota
│   ├── modulo-centros        ← Terminales y puertos
│   ├── modulo-auth           ← Autenticación y RBAC
│   ├── modulo-magyp          ← Integración AFIP/MAGYP
│   ├── modulo-mtr            ← MTR / MATba / Rofex
│   ├── modulo-turneada       ← Sistema de turnos
│   ├── modulo-fertilizantes  ← Cupos de fertilizantes
│   ├── modulo-agroquimicos   ← Pedidos de agroquímicos
│   ├── modulo-bot            ← WhatsApp ChatBot
│   └── modulo-erp            ← Integración ERP
│
├── 03-servicios-backend/     ← Servicios y componentes técnicos
│   ├── _indice-servicios     ← Índice de servicios
│   ├── servicio-jwt          ← Autenticación JWT
│   ├── servicio-queue        ← Cola async (yii2-queue)
│   ├── servicio-notificaciones ← SMS, WhatsApp, push
│   ├── servicio-pdf-excel    ← Generación de documentos
│   ├── servicio-integracion-afip ← Integración AFIP/CTG
│   └── servicio-socket       ← WebSocket / ElephantIO
│
├── 04-modelo-de-datos/       ← Entidades y base de datos
│   ├── _indice-entidades     ← Catálogo de 84 modelos
│   └── diagrama-er-global    ← Diagrama ER completo (Mermaid)
│
├── 05-inventarios/           ← Inventarios técnicos
│   ├── tree-estructura-archivos   ← Árbol de 170+ controllers
│   ├── functional-classification  ← 30 módulos clasificados
│   ├── reports-and-wizards-inventory ← 8 reportes + 9 wizards
│   ├── data-files-index      ← SQL, 623 migraciones, config
│   ├── cross-module-dependencies ← Grafo de dependencias
│   ├── depends-matrix        ← Matriz NxN de dependencias
│   ├── core-vs-custom-dependencies ← Composer deps categorizadas
│   └── security-inventory    ← 13 hallazgos de seguridad
│
├── 06-flujos-transversales/  ← Flujos de negocio end-to-end
│   ├── _indice-flujos        ← Índice de flujos
│   ├── flujo-alta-cupo       ← Ciclo de vida completo del cupo
│   ├── flujo-carta-porte     ← Emisión CPe con AFIP
│   ├── flujo-autenticacion   ← Login JWT + RBAC
│   └── flujo-bot-chofer      ← WhatsApp bot flow
│
├── 07-operacion-y-despliegue/ ← DevOps y configuración
│   ├── despliegue-docker     ← Docker Compose, comandos
│   └── configuracion-entorno ← params.php, main-local.php
│
└── 08-riesgos-y-deuda-tecnica/ ← Análisis de riesgo
    ├── hotspots              ← Archivos críticos (v3/CupoController 5754 líneas)
    ├── deuda-tecnica         ← Catálogo de 11 items de deuda
    └── recomendaciones-modernizacion ← PHP 8.2, Services, tests
```

---

## 00 — Overview

- [[vision-general]] — Qué es Muvinapp, dominio de negocio, actores, alcance del repo
- [[arquitectura-alto-nivel]] — Diagrama completo del sistema, módulos, integraciones externas
- [[stack-tecnologico]] — Yii2 2.0.48, PHP, JWT, Queue, Docker, todas las deps
- [[glosario]] — Cupo, CTG, CPe, CUIT, todos los actores y estados

---

## 01 — Módulos

[[_indice-modulos]] — Índice completo con mapa mental

### Core de negocio
- [[modulo-cupos]] — Dominio base de cupos (9 controllers)
- [[modulo-v3]] — V3: cupos + Carta de Porte Electrónica (10 controllers, 50+ endpoints)
- [[modulo-viajes]] — Viajes y solicitudes de transporte (15 controllers)
- [[modulo-choferes]] — Gestión de choferes y equipos (11 controllers)
- [[modulo-centros]] — Terminales y puertos (14 controllers)

### Integraciones externas
- [[modulo-magyp]] — AFIP/MAGYP (6 controllers)
- [[modulo-mtr]] — MATba/Rofex (1 controller)
- [[modulo-erp]] — ERP via Bus Integración (2 controllers)

### Módulos especializados
- [[modulo-fertilizantes]] — Cupos fertilizantes con Excel (4 controllers)
- [[modulo-agroquimicos]] — Pedidos agroquímicos (1 controller)
- [[modulo-turneada]] — Sistema de turnos (4 controllers)
- [[modulo-bot]] — WhatsApp ChatBot (2+ controllers)
- [[modulo-auth]] — JWT + RBAC (5+ controllers)

### Legacy
- [[modulo-v2]] — V2 legacy sin CPe (2 controllers) 🟡

---

## 03 — Servicios Backend

[[_indice-servicios]]

- [[servicio-jwt]] — Autenticación, roles, tabla access_tokens
- [[servicio-queue]] — Cola MySQL, CurlJob, AsyncCurl
- [[servicio-notificaciones]] — SMS Infobip, WhatsApp, WebSocket, BD
- [[servicio-pdf-excel]] — mPDF, PhpSpreadsheet, spatie/pdf-to-image
- [[servicio-integracion-afip]] — Microservicio AFIP, CTG, CPe
- [[servicio-socket]] — ElephantIO, Socket.IO 🔴 (sin versión)

---

## 04 — Modelo de Datos

- [[diagrama-er-global]] — Diagrama ER Mermaid con entidades principales
- [[_indice-entidades]] — Los 84 modelos ActiveRecord catalogados

---

## 05 — Inventarios

- [[tree-estructura-archivos]] — Árbol completo + 170 controllers clasificados
- [[functional-classification]] — 30 grupos funcionales con gráficos
- [[reports-and-wizards-inventory]] — 8 reportes + 9 wizards
- [[data-files-index]] — SQL scripts, 623 migraciones, archivos config
- [[cross-module-dependencies]] — Grafo de dependencias entre módulos
- [[depends-matrix]] — Matriz NxN de dependencias
- [[core-vs-custom-dependencies]] — Análisis de Composer deps
- [[security-inventory]] — **13 hallazgos** (3 🔴 críticos)

---

## 06 — Flujos Transversales

[[_indice-flujos]]

- [[flujo-alta-cupo]] — Publicación → demanda → asignación → activación → descarga
- [[flujo-carta-porte]] — Emisión CPe con AFIP, campos obligatorios, ciclo de vida
- [[flujo-autenticacion]] — Login → JWT → RBAC → acción
- [[flujo-bot-chofer]] — WhatsApp → webhook → estado conversacional → cupo

---

## 07 — Operación y Despliegue

- [[despliegue-docker]] — Docker Compose, multi-stage build, health check, comandos
- [[configuracion-entorno]] — params.php, main-local.php, CORS, variables de entorno

---

## 08 — Riesgos y Deuda Técnica

- [[hotspots]] — Top archivos críticos (CupoController 5,754 líneas)
- [[deuda-tecnica]] — 11 items catalogados con severidad y esfuerzo
- [[recomendaciones-modernizacion]] — Roadmap: PHP 8.2, Services, SwiftMailer, tests

---

## Alertas de seguridad activas

> [!danger] SEC-001: Rotar inmediatamente
> `params.php` contiene `'s-roles-permisos.token' => '123456'`
> Ver [[security-inventory]] para acción requerida.

> [!warning] SEC-002: Log en repo
> `console/log.cronAfip.txt` committado al repositorio.
> Agregar a `.gitignore` y purgar historial.

> [!warning] SEC-003: ElephantIO sin versión
> `backend/ElephantIO/` sin control Composer → vulnerabilidades desconocidas.

---

## Números del sistema

| Métrica | Valor |
|---------|-------|
| Controladores totales | ~170 |
| Modelos ActiveRecord | 84 |
| Migraciones | 623+ |
| Módulos documentados | 14 |
| Hallazgos de seguridad | 13 (3 críticos) |
| Items de deuda técnica | 11 |
| Líneas en CupoController v3 | 5,754+ |
| Años de desarrollo activo | 8+ |
| Framework | Yii2 2.0.48 |
| Runtime | PHP 8.2 (Docker) |
| DB | MySQL/MariaDB |
