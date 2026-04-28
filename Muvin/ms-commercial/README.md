# Documentación del Microservicio Comercial (ms-commercial)

> [!info] Última actualización: 2026-04-27
> Stack: NestJS 11.1.8 + Prisma 6.19.0 + MySQL | API RPC sobre TCP:4005

Bienvenido a la documentación completa del **ms-commercial**, microservicio de gestión de contratos comerciales en el ecosistema MUVIN.

## Navegación Rápida

### 📋 Inicio Aquí
- [[00-overview/vision-general|Visión General]] - Qué es este microservicio y para qué sirve
- [[00-overview/stack-tecnologico|Stack Tecnológico]] - Tecnologías, versiones y configuración
- [[00-overview/glosario|Glosario]] - Términos clave del negocio y la arquitectura

### 🏗️ Arquitectura y Diseño
- [[00-overview/arquitectura-alto-nivel|Arquitectura de Alto Nivel]] - Diagrama del sistema y componentes
- [[01-modulos/_indice-modulos|Índice de Módulos]] - CoreModule, ContractsModule, ValidationModule
- [[06-flujos-transversales/_indice-flujos|Flujos Transversales]] - Cómo fluyen los datos entre módulos

### 📦 Desarrollo
- [[01-modulos/modulo-contracts|Módulo Contracts]] - Gestión de contratos comerciales
- [[01-modulos/modulo-validation|Módulo Validation]] - Autenticación y validación de claves
- [[02-funcionalidades/_indice-funcionalidades|Índice de Funcionalidades]] - Todas las acciones RPC (7 + 5)

### 🔧 Operación
- [[07-operacion-y-despliegue/requisitos-entorno|Requisitos de Entorno]] - Node, MySQL, Docker
- [[07-operacion-y-despliegue/build-y-despliegue|Build y Despliegue]] - CI/CD, Docker, GitHub Actions
- [[07-operacion-y-despliegue/configuracion|Configuración]] - Variables de entorno, secretos

### 📊 Modelo de Datos
- [[04-modelo-de-datos/diagrama-er-global|Diagrama E-R Global]] - Entidades y relaciones
- [[04-modelo-de-datos/_indice-entidades|Índice de Entidades]] - 5 tablas principales

### 🔍 Auditoría e Inventarios
- [[05-inventarios/tree-estructura-archivos|Árbol de Estructura de Archivos]] - Mapeo completo del código
- [[05-inventarios/cross-module-dependencies|Dependencias Entre Módulos]] - Gráfico de acoplamiento
- [[05-inventarios/depends-matrix|Matriz de Dependencias]] - Tabla de quién depende de quién
- [[05-inventarios/security-inventory|Inventario de Seguridad]] - API keys, secretos, autenticación

### ⚠️ Mantenimiento
- [[08-riesgos-y-deuda-tecnica/hotspots|Hotspots Críticos]] - Áreas de alto riesgo
- [[08-riesgos-y-deuda-tecnica/deuda-tecnica|Deuda Técnica]] - Problemas conocidos y mejoras pendientes
- [[08-riesgos-y-deuda-tecnica/recomendaciones-modernizacion|Recomendaciones de Modernización]] - Plan de evolución

---

## 📁 Estructura de la Documentación

```
docs/
├── 00-overview/              # Contextualización del proyecto
│   ├── vision-general.md
│   ├── arquitectura-alto-nivel.md
│   ├── stack-tecnologico.md
│   └── glosario.md
│
├── 01-modulos/               # Descripción de módulos NestJS
│   ├── _indice-modulos.md
│   ├── modulo-contracts.md
│   └── modulo-validation.md
│
├── 02-funcionalidades/       # Cada acción RPC documentada
│   ├── _indice-funcionalidades.md
│   ├── contracts-*.md        # 7 acciones del módulo Contracts
│   └── validation-*.md       # 5 acciones del módulo Validation
│
├── 03-servicios-backend/     # Endpoints y contratos
│   ├── _indice-servicios.md
│   └── commercial-endpoints.md
│
├── 04-modelo-de-datos/       # Schema Prisma + MySQL
│   ├── _indice-entidades.md
│   ├── diagrama-er-global.md
│   └── entidad-*.md          # Una por cada tabla
│
├── 05-inventarios/           # Auditoría de código
│   ├── tree-estructura-archivos.md
│   ├── cross-module-dependencies.md
│   ├── depends-matrix.md
│   ├── functional-classification.md
│   ├── core-vs-custom-dependencies.md
│   ├── reports-and-wizards-inventory.md
│   ├── security-inventory.md
│   └── data-files-index.md
│
├── 06-flujos-transversales/  # Data flow end-to-end
│   ├── _indice-flujos.md
│   ├── flujo-creacion-contrato.md
│   └── flujo-validacion-autenticacion.md
│
├── 07-operacion-y-despliegue/ # DevOps y deployment
│   ├── requisitos-entorno.md
│   ├── build-y-despliegue.md
│   └── configuracion.md
│
└── 08-riesgos-y-deuda-tecnica/ # Mantenimiento
    ├── hotspots.md
    ├── deuda-tecnica.md
    └── recomendaciones-modernizacion.md
```

---

## 🚀 Guía para Nuevos Desarrolladores

### Día 1: Comprensión General
1. Lee [[00-overview/vision-general|Visión General]] (5 min)
2. Lee [[00-overview/stack-tecnologico|Stack Tecnológico]] (5 min)
3. Mira [[00-overview/arquitectura-alto-nivel|Arquitectura de Alto Nivel]] y el diagrama (10 min)
4. Consulta [[00-overview/glosario|Glosario]] según necesites

### Día 2: Arquitectura del Código
1. Revisa [[01-modulos/_indice-modulos|Índice de Módulos]] (10 min)
2. Lee [[01-modulos/modulo-contracts|Módulo Contracts]] (15 min)
3. Lee [[01-modulos/modulo-validation|Módulo Validation]] (10 min)
4. Consulta [[05-inventarios/tree-estructura-archivos|Árbol de Archivos]] para orientarte en el repo

### Día 3: Funcionalidades Específicas
1. Identifica qué funcionalidad necesitas implementar/entender
2. Consulta [[02-funcionalidades/_indice-funcionalidades|Índice de Funcionalidades]]
3. Lee el documento específico (ej: [[02-funcionalidades/contracts-create|contracts-create.md]])
4. Revisa el código fuente referenciado en el documento

### Antes de Hacer Cambios en BD
1. Lee [[04-modelo-de-datos/diagrama-er-global|Diagrama E-R Global]]
2. Consulta la entidad específica (ej: [[04-modelo-de-datos/entidad-contracts|entidad-contracts.md]])
3. Verifica [[05-inventarios/depends-matrix|Matriz de Dependencias]] para impacto

### Para Deployment
1. Revisa [[07-operacion-y-despliegue/configuracion|Configuración]]
2. Lee [[07-operacion-y-despliegue/build-y-despliegue|Build y Despliegue]]
3. Consulta [[05-inventarios/security-inventory|Inventario de Seguridad]] para secretos

---

## 🔗 Enlaces Rápidos

| Recurso | Ubicación |
|---------|-----------|
| **Código Fuente** | `c:\Proyectos\bcr-muvin\ms-commercial\src` |
| **Prisma Schema** | `c:\Proyectos\bcr-muvin\ms-commercial\prisma\schema.prisma` |
| **Configuración NestJS** | `c:\Proyectos\bcr-muvin\ms-commercial\src\app.module.ts` |
| **Tests** | `c:\Proyectos\bcr-muvin\ms-commercial\test` |
| **Docker** | `c:\Proyectos\bcr-muvin\ms-commercial\Dockerfile` |
| **CI/CD** | `c:\Proyectos\bcr-muvin\ms-commercial\.github\workflows` |

---

## 📞 Información de Contacto

- **Desarrollador Principal:** Rodolfo Vázquez
- **Team:** MUVIN Development
- **Repositorio:** https://github.com/muvin-com/ms-commercial
- **Servidor:** docker-registry.alternativasinteligentes.com (puerto 4005)

---

## 📌 Convenciones de Esta Documentación

- **[[links]]** - Enlaces internos entre documentos (formato Obsidian)
- **`código`** - Código fuente, archivos o comandos
- **🟢 BAJO** - Riesgo/severidad bajo
- **🟡 MEDIO** - Riesgo/severidad medio
- **🔴 ALTO** - Riesgo/severidad alto
- **⚠️ CRÍTICO** - Riesgo/severidad crítico
- **> [!info]** - Información auxiliar
- **> [!warning]** - Advertencias importantes
- **> [!danger]** - Peligros que pueden causar daño

---

## 📈 Estadísticas del Proyecto

| Métrica | Valor |
|---------|-------|
| Módulos NestJS | 3 (Core, Contracts, Validation) |
| Acciones RPC | 12 (7 Contracts + 5 Validation) |
| Tablas MySQL | 5 (contracts, entities, products, roles, keys) |
| Repositorios | 4 (Contracts, Entities, Products, Keys) |
| Microservicios Externos | 3 (ms-auth, ms-integrations, ms-logs) |
| Versión Node.js Mínima | 20.x |
| Antigüedad del Proyecto | ~1 año (última migración: Nov 2025) |

