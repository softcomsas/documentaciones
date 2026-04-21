# Glosario de Dominio

> **Contexto:** [[vision-general]] · [[README]]

## Términos de negocio (dominio agrícola)

| Término | Definición en contexto |
|---------|----------------------|
| **cupo** | Cupo de entrega de granos — autorización de cantidad máxima a entregar en una planta |
| **CTG** | Carta de Porte Electrónica — documento digital obligatorio para el transporte de granos en Argentina |
| **carta de porte** | Documento que acompaña el traslado de granos (física o electrónica) |
| **transportista** | Empresa o persona responsable del transporte de granos |
| **chofer** | Conductor del camión. Se registra por CUIT o nombre |
| **dominio** | Patente del camión tractor |
| **dominioAcoplado** | Patente del acoplado/semi-remolque |
| **cosecha** | Campaña agrícola (ej: `2024/2025`) |
| **grano** | Tipo de cereal (maíz, soja, girasol, trigo, etc.) |
| **destino** | Planta o punto de descarga del grano |
| **plantaRuca** | Código interno de la planta receptora en el sistema RUCA (Registro Único de Camiones Agrícolas) |
| **codGrano** | Código numérico del grano según nomenclatura del sistema legado |
| **desvio** | Desvío de ruta o cambio de destino de un envío |
| **nroContrato** | Número de contrato de compra/venta de granos |
| **demandaCupo** | Solicitud de asignación de cupo de entrega |
| **creadoPor** | Usuario que originó la operación |

## Términos técnicos del MS

| Término | Definición |
|---------|-----------|
| **hash** | Correlation ID generado por el caller. String único de hasta 50 chars que identifica una operación a través de su ciclo de vida |
| **trace** | Registro de una operación GraphQL completa. Agrupa todos sus eventos. |
| **event** | Registro de una llamada individual a un microservicio dentro de una traza |
| **legacy log** | Registro de un request HTTP hacia los sistemas legados (PANEL o DESCARGAS) |
| **LEGACY_PANEL** | Sistema legado que gestiona el panel de operaciones de la plataforma |
| **LEGACY_DESCARGAS** | Sistema legado que gestiona las descargas de granos en planta |
| **search_terms** | Campo `VARCHAR` con valores de negocio extraídos y concatenados para búsqueda. Ej: `"C123456 maiz ABC-123 20123456789"` |
| **Brotli Q11** | Algoritmo de compresión de Google a calidad máxima (11/11). Se usa para comprimir payloads/responses antes de guardar en BD |
| **fire & forget** | Patrón de comunicación donde el emisor no espera respuesta. En NestJS TCP se implementa con `ClientProxy.emit()` |
| **PENDING** | Estado inicial de una traza o log. Indica que el ciclo de vida no se ha completado |
| **TCP transport** | Protocolo de transporte de NestJS Microservices. Comunicación binaria sobre TCP sin HTTP |

## Abreviaturas

| Abreviatura | Significado |
|-------------|-------------|
| MS | Microservicio |
| GW | Gateway (API Gateway NestJS) |
| BD | Base de datos |
| CMD | Message pattern / comando TCP |
| DX | Developer Experience |

---

*Ver también: [[vision-general]] · [[README]] · [[arquitectura-alto-nivel]]*
