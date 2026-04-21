# Inventario de reportes y wizards

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Resultado del inventario

> [!info] Sin reportes ni wizards
> `muvin-ms-legacy` es un microservicio de integración pura. **No contiene reportes, wizards, ni interfaces de usuario.**
>
> Su única responsabilidad es actuar como proxy tipado entre otros microservicios del ecosistema Muvin (que se comunican por TCP) y el backend legacy REST de Muvin.

## Inventario de endpoints expuestos (única funcionalidad)

| Tipo | Nombre | Propósito | Estado |
|------|--------|-----------|--------|
| Integración | `comprador-by-razon-social` | Busca compradores en el sistema legacy por razón social | 🟢 Activo |
| Integración | `comprador-by-cuit` | Busca compradores por CUIT | ⚠️ Declarado en tipos, sin implementar |

## Notas para auditores

- No hay archivos `.rdf`, `.fmb`, `.rdl`, `.jasper`, ni similares.
- No hay carpetas de reportes, plantillas ni dashboards.
- La funcionalidad de "consulta" está completamente delegada al backend legacy externo (`https://dev.muvinapp.com/api/backend/web/`).

> Ver [[functional-classification]] para la clasificación completa de componentes.
