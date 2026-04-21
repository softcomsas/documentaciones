# Inventario de Reportes y Wizards — `muvin-ms-logs`

> **Última revisión:** 2026-04-21

---

## Resultado del inventario

> [!info] Sin reportes ni wizards
> `muvin-ms-logs` es un **microservicio de logging puro**. No genera reportes, no tiene asistentes de múltiples pasos y no expone interfaces de usuario.
>
> Su función es exclusivamente **recibir, comprimir y persistir** trazas de auditoría provenientes de otros servicios del ecosistema Muvin/BCR.

---

## Capacidades de consulta disponibles (no son reportes)

Aunque no hay reportes formales, el módulo `LegacyModule` expone tres operaciones de **búsqueda/consulta** que permiten recuperar registros almacenados:

| Operación | Pattern TCP | Propósito | Tipo |
|-----------|-------------|-----------|------|
| `searchId` | `logs.legacy.search.id` | Recupera un registro legacy por su ID primario | Consulta puntual |
| `searchUser` | `logs.legacy.search.user` | Lista registros de un usuario para un endpoint+método específico, con paginación | Consulta paginada |
| `searchTerms` | `logs.legacy.search.terms` | Búsqueda full-text por términos de negocio extraídos del payload | Búsqueda semántica |

> Estas operaciones **no son reportes** en sentido estricto — son APIs de consulta expuestas vía TCP para ser consumidas por otros servicios (presumiblemente el gateway o un panel de administración).

---

## Inventario formal (tabla vacía documentada)

| Nombre | Propósito | Archivo origen | Datos consumidos | Módulo | Estado |
|--------|-----------|----------------|-----------------|--------|--------|
| — | — | — | — | — | ❌ No aplica |

---

*Ver también: [[functional-classification]] · [[modulo-legacy]] · [[legacy-search-id]] · [[legacy-search-user]] · [[legacy-search-terms]]*
