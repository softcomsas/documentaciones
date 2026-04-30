# Módulo: Shared

> **Ruta/Namespace:** `src/app/shared/`
> **Criticidad:** 🟢 Baja
> **Estado:** Activo

## Propósito

Módulo transversal que agrupa todos los recursos reutilizables de la aplicación: layouts, servicios globales, guards, interceptors, pipes, directivas, componentes de UI genéricos (~24 servicios), ~90 modelos/interfaces TypeScript de dominio, utilidades y validadores. Es importado por todos los módulos de páginas.

## Recursos que expone

### Servicios
| Servicio | Propósito |
|----------|-----------|
| `GlobalService` | Configuración global: `apiHost`, helpers básicos |
| `UserService` | Datos del usuario logueado (persona, rol) |
| `NomencladoresService` | Catálogos y listas de valores del sistema |
| `ListaReservaService` | Estado compartido de la lista de reservas |
| `DataSharedService` | Comunicación de datos entre componentes no relacionados |
| `MessageService` | Servicio de mensajes/notificaciones |
| `ErrorHandlingService` | Manejo centralizado de errores HTTP |
| `FileUploadService` | Upload de archivos |
| `ExcelService` | Exportación a Excel (`file-saver`) |
| `LayoutService` | Estado del layout (sidebar abierto/cerrado) |
| `ThemeService` | Gestión de temas visuales |
| `NavigationService` | Items de navegación del sidebar |
| `RoutePartsService` | Partes de la ruta actual (breadcrumbs) |
| `MatchMediaService` | Detección de breakpoints responsive |
| `CustomizerService` | Personalización de UI |
| Dialogs | `AppAlertService`, `AppConfirmService`, `AppErrorService`, `AppAtencionService`, `AppLoaderService` |

### Guards
| Guard | Propósito |
|-------|-----------|
| `CentroAuthGuard` | Protege rutas privadas. Valida token y rol (`3`, `11`, `16`) |

### Layouts
| Componente | Ruta | Uso |
|------------|------|-----|
| `AdminLayoutComponent` | — | Layout con sidebar para rutas autenticadas |
| `AuthLayoutComponent` | — | Layout limpio para login/registro |

### Modelos de dominio (~90 interfaces)
Ver árbol completo en [[tree-estructura-archivos]]. Algunos modelos clave:
- `pedido.ts`, `pedidoModel.ts` — Pedido de despacho
- `reserva` (varios archivos) — Reserva de cupo
- `camion.ts`, `chofer.ts`, `acoplado.ts` — Vehículos y choferes
- `origen.model.ts`, `destino.ts` — Puntos de origen/destino
- `producto.ts`, `fertilizantes.model.ts` — Productos
- `user.ts`, `user.model.ts`, `persona.model.ts` — Usuarios y personas

## Dependencias

- **Depende de:** ningún módulo interno (es la base)
- **Es usado por:** [[modulo-sessions]], [[modulo-pedidos]], [[modulo-transportistas]]

## Riesgos y deuda técnica detectados

- ⚠️ `ErrorHandlingService` existe pero no todos los servicios lo usan consistentemente (algunos usan su propia función `handleError` privada).
- ⚠️ Cantidad excesiva de modelos (~90 interfaces). Varios parecen ser duplicados o variantes del mismo concepto (ej: `camion.ts` en shared y `camion.ts` en pedidos/models).
- ⚠️ `inmemory-db/` presente en shared — sugiere que en algún momento se usó `angular-in-memory-web-api` para mocking. 🚧 Verificar si aún está activo en producción.

## Archivos fuente relevantes

- `src/app/shared/shared.module.ts`
- `src/app/shared/shared-material.module.ts`
- `src/app/shared/guards/centro-auth.guard.ts`
- `src/app/shared/services/`
- `src/app/shared/models/`
- `src/app/shared/components/layouts/`
- `src/app/shared/interceptors/`
- `src/app/shared/pipes/`
- `src/app/shared/directives/`
- `src/app/shared/validators/`
