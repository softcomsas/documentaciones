# Glosario — App Agronomy

> **Última revisión:** 2026-04-30

## Términos de negocio

| Término | Definición |
|---------|-----------|
| **Centro** | Centro agroindustrial. Entidad que recibe y procesa la carga (granos, fertilizantes). Rol `3` en el sistema. |
| **Pedido** | Solicitud formal de despacho de mercadería (granos o fertilizantes) emitida por un dador de carga. |
| **Reserva** | Cupo asignado para el retiro de una cantidad de producto en una fecha y origen determinados. |
| **Cupo** | Capacidad de carga disponible en un origen/terminal para una fecha dada. |
| **Origen** | Punto de carga o terminal desde donde se despacha la mercadería. |
| **Destino** | Punto de entrega de la mercadería. |
| **Dador de carga** | Empresa o persona que genera el pedido (quien tiene la mercadería). Rol `5`. |
| **Transportista** | Empresa o persona que realiza el transporte. Propietaria de camiones y choferes. |
| **Chofer** | Conductor del camión asignado a un pedido/viaje. |
| **Camión** | Vehículo de carga identificado por patente. |
| **Acoplado** | Remolque adicional acoplado al camión. |
| **Equipo** | Combinación camión + acoplado (y eventualmente semirremolque). |
| **Capacidad de terminal** | Cupos disponibles en una terminal para fechas futuras. |
| **Mezcla / Fertilizante** | Tipo de pedido específico para productos de fertilización. |
| **Despacho directo** | Asignación directa de camión sin turno previo. |
| **Tipo de despacho** | Modalidad de retiro: turno, directo, etc. |
| **Seguimiento** | Vista de estado de las reservas y movimientos del pedido. |
| **CUIT** | Clave Única de Identificación Tributaria (Argentina). Identifica empresas y personas. |
| **Razón social** | Nombre legal de una empresa en Argentina. |
| **Prepedido** | Estado previo a la confirmación del pedido. |

## Términos técnicos

| Término | Definición |
|---------|-----------|
| **SPA** | Single Page Application. La app no recarga la página; navega internamente. |
| **PWA** | Progressive Web App. Puede instalarse como app nativa y funcionar con conectividad limitada. |
| **Lazy loading** | Los módulos se cargan bajo demanda (al navegar), no al iniciar la app. |
| **BehaviorSubject** | Tipo especial de Observable de RxJS que mantiene el último valor emitido. Usado como "micro-store" en algunos servicios. |
| **Guard** | Clase Angular que intercepta la navegación y puede redirigir si el usuario no cumple condiciones (ej: no autenticado). |
| **Interceptor** | Clase Angular que modifica peticiones HTTP salientes o respuestas entrantes (ej: agregar token). |
| **environment** | Archivos `environment.ts` / `environment.prod.ts` que definen variables de configuración por ambiente. |
| `apiURL` | Variable de `environment` que apunta al host de la API REST. |
| **rol** | Clave en `localStorage` con el rol numérico del usuario logueado. Condiciona qué pantallas y endpoints son accesibles. |
| **token** | JWT o token de sesión almacenado en `localStorage`. Se usa para autenticar peticiones al backend. |
