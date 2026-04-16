# Inventario de Seguridad

> **Última revisión:** 2026-04-16
> **Hallazgos totales:** 20
> **Críticos (🔴):** 10 | **Medios (🟡):** 10
> **Estado general:** ⛔ Requiere remediación urgente

---

## Resumen ejecutivo

El panel presenta **vulnerabilidades severas** en autenticación, manejo de datos sensibles y dependencias. El framework base (Angular 6) lleva **~7 años sin soporte de seguridad**. La combinación de `eval()` activo, RBAC puramente client-side y JWT en localStorage constituye una superficie de ataque crítica.

---

## Matriz de hallazgos

| # | Hallazgo | Severidad | Categoría | Archivo(s) |
|---|---|---|---|---|
| 1 | RBAC guards leen `rol` de localStorage (escalación trivial) | 🔴 | Auth | `shared/services/auth/*.guard.ts` |
| 2 | Token expiration check comentado en TODOS los guards | 🔴 | Auth | `shared/services/auth/*.guard.ts` |
| 3 | 9 instancias de `eval()` con input dinámico | 🔴 | XSS/Injection | Ver tabla abajo |
| 4 | Angular 6 EOL + decenas de deps obsoletas | 🔴 | Dependencies | `package.json` |
| 5 | JWT + refresh token en localStorage | 🔴 | Token Storage | `shared/services/auth.service.ts` |
| 6 | CUIT/CUIL (ID fiscal PII) en localStorage | 🔴 | Data Exposure | `shared/services/utils/set-local-storage.ts` |
| 7 | Sin protección CSRF/XSRF | 🔴 | API Security | Global |
| 8 | API keys de Firebase + Google Maps hardcodeadas | 🔴 | Secrets | Environments + módulos |
| 9 | 43+ items sensibles en localStorage | 🔴 | Data Exposure | `set-local-storage.ts` |
| 10 | Manipulación directa de `innerHTML` en DOM | 🔴 | XSS | Grid components |
| 11 | `status = 1` (assignment, no comparison) en login | 🟡 | Auth Logic | `auth.service.ts`, `signin.component.ts` |
| 12 | Token enviado como query parameter | 🟡 | Token Leakage | `file-upload.service.ts` |
| 13 | `bypassSecurityTrustUrl` usage | 🟡 | XSS | `file-upload.service.ts` |
| 14 | `[innerHTML]` con mensajes de error del servidor | 🟡 | XSS | `app-confirm`, `app-atencion` |
| 15 | Prod environment apunta a URLs dev/localhost | 🟡 | Config | `environment.prod.ts` |
| 16 | Firebase mismo proyecto en dev y prod | 🟡 | Isolation | Environments |
| 17 | Cookies sin flags Secure/HttpOnly/SameSite | 🟡 | Cookies | `cookies.service.ts` |
| 18 | Headers estáticos con token stale al load | 🟡 | Auth | `auth.service.ts` |
| 19 | Logout no limpia todos los items almacenados | 🟡 | Data Cleanup | `delete-local-storage.ts` |
| 20 | `/destinatario` solo usa AuthGuard (sin role check) | 🟡 | Access Control | `app.routing.ts` |

---

## 1. Autenticación y autorización

### 🔴 Guards con RBAC puramente client-side

**Todos** los guards de rol leen `localStorage.getItem('rol')` — un valor **escribible desde DevTools**:

```js
// Escalación de privilegios instantánea:
localStorage.setItem('rol', '1'); // → acceso admin
```

| Guard | Roles permitidos (`rol`) | Rutas protegidas |
|---|---|---|
| `AdminAuthGuard` | `1`, `12` | `/admin` |
| `CentroAuthGuard` | `3`, `11`, `16` | `/centro`, `/ccpp` |
| `DadorAuthGuard` | `5` | `/dador` |
| `DestinoAuthGuard` | `7` | `/destino` |
| `TranspAuthGuard` | `4` | (importado, no usado en routing) |
| `MagypAuthGuard` | `14` | `/magyp` |
| `MtrAuthGuard` | `3`, `16` | `/mtr` |
| `FertilizantesAuthGuard` | `15` | `/fertilizante` |

> [!danger] Escalación de privilegios
> El JWT nunca se decodifica ni se valida client-side. El rol nunca se extrae del token. Cualquier usuario autenticado puede escalar a cualquier rol modificando localStorage.

### 🔴 Token expiration check deshabilitado

En **todos** los guards, el check de expiración está **comentado**:

```typescript
/* if (dateExpires < now) {
  this.router.navigate(['/sessions/signin']);
  return false;
} */
```

Impacto: un token robado otorga acceso indefinido.

### 🟡 Bug en login: asignación en vez de comparación

```typescript
// auth.service.ts y signin.component.ts
if ((res.status = 1)) {  // = en vez de ===
```

Siempre es truthy — el check de status nunca valida realmente la respuesta.

---

## 2. Secretos hardcodeados

### 🔴 Firebase API Key en código fuente

```
// environment.ts y environment.prod.ts (idénticos)
apiKey: "AIzaSyDIdkEFe3Yph2bwYzT-eSui5wnPG8ylg6A"
projectId: "muvinapp-89561"
appId: "1:431066179758:web:89490ace18dced12"
```

### 🔴 Google Maps API Key en módulos

Misma key en 3 archivos:

| Archivo | Key |
|---|---|
| `views/dador/dador.module.ts` | `AIzaSyBKesIXvH_yWq7YhJoEZ8sh3snbHWWd3MM` |
| `views/marketing/marketing.module.ts` | Misma key |
| `views/destinatario/destinatario.module.ts` | Misma key |

### 🟡 Prod apunta a dev/localhost

```typescript
// environment.prod.ts
apiHost: "https://dev.muvinapp.com/api/backend/web/"  // ← dev subdomain
URL_SERVICIOS: "http://localhost/api_muvin"            // ← HTTP + localhost
```

---

## 3. XSS e inyección de código

### 🔴 9 instancias de `eval()` activo

| Archivo | Línea | Patrón |
|---|---|---|
| `views/destino/panel/panel.component.ts` | 134 | `eval('miobject.'+this.dynamicColumns[index].columnDef+'=0')` |
| `shared/components/home/solicitudes/solicitudes.component.ts` | 234 | `eval('this.filtro.' + param + ' = val')` |
| `views/admin/chofer-vencido/chofer-vencido.component.ts` | 106 | Mismo patrón |
| `shared/components/home/carga-pedido/carga-pedido.component.ts` | 2356 | Mismo patrón |
| `views/admin/trabajadores/trabajadores.component.ts` | 279 | Mismo patrón |
| `views/admin/mostrar-logs/mostrar-logs.component.ts` | 110 | Mismo patrón |
| `views/admin/rankings/rankings.component.ts` | 183 | `eval('this.' + opt + ' = true;')` |
| `views/admin/rankings/listado-rechazados/listado-rechazados.component.ts` | 75 | Mismo patrón |
| `views/admin/rankings/listado-desviados/listado-desviados.component.ts` | 75 | Mismo patrón |

**Remediación:** Reemplazar por notación de corchetes: `this.filtro[param] = val`

### 🔴 Manipulación directa de innerHTML

| Archivo | Descripción |
|---|---|
| `views/cupera/components/asignacion-v5/.../grid-solicitudes-v5.component.ts` | `td.innerHTML = "<button..."` |
| `shared/components/cupo/asignacion-v2/asignacion-v2.component.ts` | Mismo patrón |
| `shared/components/cupo/cupera3/.../grid-solicitudes.component.ts` | Mismo patrón |

### 🟡 `[innerHTML]` con mensajes del servidor

El error interceptor pasa mensajes del servidor a dialogs que renderizan con `[innerHTML]`:

```
ErrorInterceptor → atencionService.confirm({ message: errorMessage }) → [innerHTML]="data.message"
```

Si el backend incluye HTML malicioso en el mensaje de error, se renderiza.

---

## 4. Datos sensibles en localStorage

### 🔴 43+ items incluyendo PII

`set-local-storage.ts` almacena:

| Categoría | Claves |
|---|---|
| **Tokens** | `token`, `refresh_token`, `expires_at` |
| **PII** | `nameUser` (razón social), `cuit_cuil` (CUIT/CUIL), `user_id` |
| **Rol/permisos** | `rol`, `nombreRol`, `roles_microservicio` |
| **Config negocio** | `usaCupera`, `usaMtr`, `tipo_turneada`, `integracion_erp`, `modulo_cerdos` |
| **Feature flags** | `esClienteFinal`, `esDadorCupo`, `esIntermediario`, `esDestinatario`, `liderTurneada` |

### 🟡 Logout no limpia todo

`delete-local-storage.ts` borra 56 items, pero **no borra** items que se setean: `user_id`, `gestion_cupos_ferti`, `acceso_formulario_arribo`, `documentacion`, `descargas`, `cupos_propios_ferti`.

---

## 5. Seguridad de API y transporte

### 🔴 Sin protección CSRF/XSRF

Zero coincidencias de `csrf`, `xsrf`, `HttpClientXsrfModule`, `HttpOnly`, `Secure`, `SameSite` en todo el código fuente.

### 🟡 Token en query parameter

```typescript
// file-upload.service.ts
let url = this.globalService.apiHost + "centro/imagen-down?r=" + token;
```

El JWT aparece en logs de servidor, historial del navegador y logs de proxy.

### 🟡 Cookies sin flags de seguridad

`cookies.service.ts` setea cookies sin `Secure`, `HttpOnly` ni `SameSite`.

---

## 6. Dependencias vulnerables

### 🔴 Framework EOL

| Paquete | Versión | EOL desde |
|---|---|---|
| `@angular/*` | 6.0.1 | Nov 2019 (~7 años) |
| `typescript` | 2.7.2 | Obsoleto |
| `zone.js` | 0.8.26 | Muy viejo |
| `node-sass` | 4.14.1 | CVEs conocidos (libsass) |
| `core-js` | 2.4.1 | EOL |

### 🔴 Paquetes con vulnerabilidades conocidas

| Paquete | Versión | Riesgo |
|---|---|---|
| `firebase` | 6.4.0 | Muy viejo, fixes de seguridad posteriores |
| `angularfire2` | 5.2.1 | Deprecado (renombrado a `@angular/fire`) |
| `html2canvas` | 1.0.0-rc.1 | Pre-release, sin parches |
| `xlsx` | 0.14.0 | CVEs en versiones viejas |
| `http` | 0.0.1-security | Placeholder de advertencia de npm |
| `http-proxy` | 1.18.1 | CVEs conocidos |
| `handsontable-pro` | 6.2.3 | Licencia cambiada, muy viejo |

---

## 7. Firebase

### 🟡 Sin archivo de reglas de seguridad

`fireservi.service.ts` escribe y lee de la colección `configuracion-panel`. No se encontró archivo de reglas de Firestore en el repositorio. Si las reglas son las default, cualquier usuario autenticado de Firebase puede leer/escribir.

### 🟡 Misma config Firebase en dev y prod

Ambos environments usan el mismo proyecto (`muvinapp-89561`). Sin aislamiento de ambientes.

---

## 8. Control de acceso por rol

### Mapa de guards vs rutas

```mermaid
graph TD
    subgraph "Guards"
        AG["AuthGuard<br/>(token exists)"]
        AAG["AdminAuthGuard<br/>(rol 1,12)"]
        CAG["CentroAuthGuard<br/>(rol 3,11,16)"]
        DAG["DadorAuthGuard<br/>(rol 5)"]
        DEG["DestinoAuthGuard<br/>(rol 7)"]
        TAG["TranspAuthGuard<br/>(rol 4)"]
        MAG["MagypAuthGuard<br/>(rol 14)"]
        MTG["MtrAuthGuard<br/>(rol 3,16)"]
        FAG["FertilizantesAuthGuard<br/>(rol 15)"]
    end

    subgraph "Rutas"
        R_ADM["/admin"]
        R_CUPO["/cupo"]
        R_CUP["/cupera"]
        R_CCPP["/ccpp"]
        R_DAD["/dador"]
        R_DEST["/destino"]
        R_FERT["/fertilizante"]
        R_MAG["/magyp"]
        R_MTR["/mtr"]
        R_DESTN["/destinatario"]
        R_SESS["/sessions"]
    end

    AAG --> R_ADM
    CAG --> R_CUPO
    CAG --> R_CUP
    CAG --> R_CCPP
    DAG --> R_DAD
    DEG --> R_DEST
    FAG --> R_FERT
    MAG --> R_MAG
    MTG --> R_MTR
    AG --> R_DESTN
    R_SESS -.->|"Sin guard"| R_SESS

    style AG fill:#ff9800
    style R_DESTN fill:#ff9800
    style R_SESS fill:#e0e0e0
```

> [!warning] Riesgo
> `/destinatario` solo verifica `AuthGuard` (que el token exista), **no verifica el rol**. Cualquier usuario autenticado puede acceder.

---

## Ruta de escalación de privilegios

```mermaid
sequenceDiagram
    participant ATK as Atacante
    participant BROWSER as DevTools
    participant GUARD as AdminAuthGuard
    participant PANEL as Panel Admin

    ATK->>ATK: Login como rol 7 (Destino)
    ATK->>BROWSER: localStorage.setItem('rol', '1')
    ATK->>GUARD: Navega a /admin
    GUARD->>GUARD: localStorage.getItem('rol') === '1' ✅
    GUARD->>GUARD: localStorage.getItem('token') exists ✅
    GUARD-->>PANEL: canActivate = true
    Note over ATK,PANEL: Acceso admin concedido.<br/>Backend puede aún validar,<br/>pero el frontend está abierto.
```

---

## Recomendaciones prioritarias

| # | Acción | Esfuerzo | Impacto |
|---|---|---|---|
| 1 | Eliminar `eval()` → usar bracket notation | Bajo | 🔴 Crítico |
| 2 | Mover tokens a `httpOnly` cookies | Medio | 🔴 Crítico |
| 3 | Implementar `HttpClientXsrfModule` | Bajo | 🔴 Crítico |
| 4 | Validar JWT y extraer rol del token en los guards | Medio | 🔴 Crítico |
| 5 | Habilitar token expiration checks | Bajo | 🔴 Crítico |
| 6 | Mover API keys a variables de entorno de CI/CD | Bajo | 🔴 Alto |
| 7 | Actualizar Angular + dependencias EOL | Muy alto | 🔴 Crítico |
| 8 | Eliminar `innerHTML` directo → usar Renderer2 | Medio | 🔴 Alto |
| 9 | Reducir datos en localStorage (mínimo PII) | Medio | 🟡 Medio |
| 10 | Configurar Firebase security rules | Bajo | 🟡 Medio |

---

## Referencias

- [[flujo-autenticacion]] — Flujo de auth completo
- [[deuda-tecnica]] — Deuda técnica consolidada
- [[hotspots]] — Archivos de mayor riesgo
- [[recomendaciones-modernizacion]] — Plan de modernización
- [[stack-tecnologico]] — Versiones del stack
