# F-01 — Grilla Semanal: Flujo End-to-End

## Descripción

Flujo completo desde que el usuario abre la app hasta ver la grilla semanal con cupos y solicitudes asignados.

## Diagrama

```
Browser abre /quota-operations/assignment
        ↓
APP_ROUTES → PrivateContainer
        ↓
DECLARATION_PRIVATE_ROUTES → QuotaOperationsContainer
        ↓
DECLARATION_QUOTA_OPERATIONS_ROUTES → AssignmentContainer
        ↓
AssignmentContainer (providedIn: AssignmentContainer)
    → instancia AssignmentService
    → llama ApiService.request('get-legacy-listado-zona', { start, taxId })
            ↓
        tokenInterceptorFn adjunta JWT
            ↓
        Backend legado responde TResultGetLegacyListadoZona
            ↓
        query.response() transforma → TData { quotas[], requests[] }
            ↓
        AssignmentService.loading = false
        AssignmentService.products computed desde TData
        AssignmentService.days computed desde start (5 días)
        ↓
AssignmentHomeView renderiza:
    ManagementComponent  ← start signal, links a filter/management
    PreviewComponent     ← computa IRow[] desde TData + days + term
        ↓
    Grilla semanal visible
        ↓
Usuario hace clic en celda (día × grupo)
    → GridQuotasComponent muestra cupos de esa celda
    → GridRequestsComponent muestra solicitudes de esa celda
```

---

# Flujo de Asignación Manual

```
Usuario en home → hace clic en cupo de GridQuotasComponent
        ↓
Navega a /assignment/management
        ↓
AssignmentManagementView
    → ApiService.request('get-legacy-buscar-para-asignar', { cupoId, ... })
    → Muestra solicitudes compatibles
        ↓
Operador selecciona solicitud y confirma
        ↓
ApiService.request('post-legacy-assign-quotas', { cupoId, solicitudId })
        ↓
AssignmentService refresca → get-legacy-listado-zona
        ↓
Router navega back a /assignment (home)
        ↓
Grilla actualizada
```

---

# Flujo de Refresh de Token

```
Request HTTP cualquiera
        ↓
tokenInterceptorFn adjunta Bearer token
        ↓
Backend responde 401 (token expirado)
        ↓
¿isRefreshing?
    NO → isRefreshing = true
         POST /refresh con refresh token
             ├── 200 → ConfigService.set('token', nuevo_token)
             │         refreshSubject.next(nuevo_token)
             │         retry request original con nuevo token
             └── error → throwError → logout
    SÍ → esperar refreshSubject.pipe(filter(t => t !== null), take(1))
         retry request con el nuevo token cuando llegue
```
