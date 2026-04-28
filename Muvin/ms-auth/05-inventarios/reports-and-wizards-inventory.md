# Inventario: Reportes y Wizards

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

> [!info] Contexto del microservicio
> `ms-auth` es un microservicio de backend puro con transporte TCP. No tiene capa de presentación propia (sin UI, sin vistas, sin reportes generados directamente). Este inventario documenta las funcionalidades orientadas a **consulta agregada o paginada** que podrían alimentar reportes en otros servicios consumidores.

---

## Reportes (consultas agregadas / paginadas)

| # | Nombre funcional | CMD | Descripción | Datos consumidos | Módulo asociado | Estado |
|---|---|---|---|---|---|---|
| R-01 | Listado paginado de contratos | `commercial.contracts.search-all` | Retorna contratos filtrados por cliente, estado y código con paginación | `contracts` (MySQL) | [[modulo-commercial]] | 🚧 Contrato definido, sin handler |
| R-02 | Búsqueda de logs por términos | `logs.legacy.search-terms` | Búsqueda textual sobre registros de log del sistema legacy | `logs_legacy` (MySQL) | [[modulo-logs]] | 🚧 Contrato definido, sin handler |
| R-03 | Búsqueda de logs por usuario | `logs.legacy.search-user` | Retorna historial de logs asociados a un usuario | `logs_legacy` (MySQL) | [[modulo-logs]] | 🚧 Contrato definido, sin handler |
| R-04 | Búsqueda de contratos por cliente | `commercial.contracts.search-list` | Listado de contratos filtrado por compañía + cliente + código | `contracts` (MySQL) | [[modulo-commercial]] | 🚧 Contrato definido, sin handler |

---

## Wizards / Asistentes

> [!info] Sin wizards detectados
> No se detectaron wizards ni asistentes multi-paso en este microservicio. La naturaleza RPC del servicio no contempla flujos guiados de UI.

---

## Observaciones

- ⚠️ Ninguno de los endpoints de consulta agregada tiene implementación de handler actualmente.
- ⚠️ El contrato `commercial.contracts.search-all` es el único que incluye paginación explícita (`IPagination`: page + limit). Los demás son búsquedas puntuales.
- ⚠️ Los parámetros de filtrado de `search-terms` están pendientes de verificar en el contrato (`logs/interfaces/legacy.ts`).
- 🚧 El esquema Prisma no está relevado en detalle — los nombres de tablas (`contracts`, `logs_legacy`) son estimados a partir de los contratos. Ver [[diagrama-er-global]].
