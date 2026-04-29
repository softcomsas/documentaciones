# F-11 — Centro de Documentación

## Descripción

Módulo principal de `documentacion-app` que centraliza la gestión de **presentaciones, documentos y aprobaciones**. El acceso a cada pestaña está controlado por roles: `DOC Presentador` y `DOC Aprobador`.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend | `documentacion-app/frontend/src/app/modules/centro-documentacion/` |
| Backend | `documentacion-app/backend/` |

## Roles de acceso

| Rol | Acceso |
|---|---|
| `DOC Presentador` | Puede crear y ver sus propias presentaciones y documentos |
| `DOC Aprobador` | Puede ver y aprobar/rechazar presentaciones pendientes |
| Ambos | Ven todas las pestañas del centro |

## Pestañas del módulo

| Pestaña | Componente | Descripción |
|---|---|---|
| Presentaciones | `presentaciones/` | Listado de presentaciones propias |
| Nueva Presentación | `nuevaPresentacion/` | Formulario para crear presentación |
| Nuevo Documento | `nuevoDocumento/` | Adjuntar documento a presentación |
| Nueva Documentación | `nuevaDocumentacion/` | Wizard completo de documentación |
| Detalle Presentación | `detallePresentacion/` | Vista de detalle con estado |
| Aprobaciones | `aprobaciones/` | Bandeja para `DOC Aprobador` |

## Flujo de usuario

```
1. CentroDocumentacionComponent se inicializa → llama documentacionService.infoUser()
2. Obtiene id y roles del usuario → guarda en localStorage
3. Determina qué pestañas mostrar:
   - aprobacionesVisible = haveRol('DOC Aprobador')
   - presentacionesVisible = haveRol('DOC Presentador')
4. selectedTabIndex = 0 (pestaña activa inicial, con delay para animación)
```

## Servicios Angular

- `DocumentacionService` — provee `infoUser()`, operaciones CRUD sobre presentaciones y documentos
- `CookiesService` — gestión de sesión
- `AlertService` — loader y mensajes de alerta

## Estado local

```typescript
roles: any[]           // roles del usuario autenticado
aprobacionesVisible: boolean
presentacionesVisible: boolean
selectedTabIndex: number
```
