# Índice: Funcionalidades

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## Módulo Auth

| ID | Funcionalidad | CMD | Tipo | Enlace |
|----|---------------|-----|------|--------|
| 1.1 | Buscar compañía por ID | `auth.companies.search.one` | Consulta | [[auth-companies-search-one]] |
| 1.2 | Buscar múltiples compañías | `auth.companies.search.all` | Consulta | [[auth-companies-search-all]] |
| 1.3 | Crear clave API | `auth.create.key` | Seguridad/CRUD | [[auth-validate-create-key]] |
| 1.4 | Generar firma | `auth.generate.signature` | Seguridad | [[auth-validate-generate-signature]] |
| 1.5 | Validar clave API | `auth.validate.key` | Seguridad | [[auth-validate-key]] |
| 1.6 | Validar autorización | `auth.validate.authorization` | Seguridad | [[auth-validate-authorization]] |
| 1.7 | Validar legacy | `auth.validate.legacy` | Seguridad/Integración | [[auth-validate-legacy]] |

---

## Módulo Commercial

| ID | Funcionalidad | CMD | Tipo | Enlace |
|----|---------------|-----|------|--------|
| 2.1 | Crear contrato | `commercial.contracts.create` | CRUD | [[commercial-contracts-create]] |
| 2.2 | Buscar contrato por referencia | `commercial.contracts.search.one` | Consulta | [[commercial-contracts-search-one]] |
| 2.3 | Listar contratos por cliente | `commercial.contracts.search.list` | Consulta | [[commercial-contracts-search-list]] |
| 2.4 | Buscar por referencia y compañía | `commercial.contracts.search.reference` | Consulta | [[commercial-contracts-search-reference]] |
| 2.5 | Listado paginado de contratos | `commercial.contracts.search.all` | Consulta/Reporte | [[commercial-contracts-search-all]] |
| 2.6 | Cambiar límite | `commercial.contracts.change.limit` | CRUD | [[commercial-contracts-change-limit]] |
| 2.7 | Cambiar balance | `commercial.contracts.change.balance` | CRUD | [[commercial-contracts-change-balance]] |

---

## Módulo Logs

| ID | Funcionalidad | CMD | Tipo | Enlace |
|----|---------------|-----|------|--------|
| 3.1 | Crear log legacy | `logs.legacy.create` | Log/Evento | [[logs-legacy-create]] |
| 3.2 | Actualizar log legacy | `logs.legacy.update` | Log/Evento | [[logs-legacy-update]] |
| 3.3 | Buscar log por ID | `logs.legacy.search.id` | Consulta | [[logs-legacy-search-id]] |
| 3.4 | Buscar logs por usuario | `logs.legacy.search.user` | Consulta | [[logs-legacy-search-user]] |
| 3.5 | Buscar logs por términos | `logs.legacy.search.terms` | Consulta/Reporte | [[logs-legacy-search-terms]] |

---

## Módulo Integrations

| ID | Funcionalidad | CMD | Tipo | Enlace |
|----|---------------|-----|------|--------|
| 4.1 | Enviar notificación email | `integrations.email.notification` | Integración | [[integrations-email-notification]] |
