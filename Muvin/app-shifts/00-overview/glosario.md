# Glosario — app-shifts

| Término | Definición |
|---------|-----------|
| **Turno** | Reserva de un horario de ingreso a un puerto o centro de carga. El chofer selecciona puerto, producto, fecha y ventanilla (horario). |
| **Turno-puerto** | Nombre del endpoint y recurso en el backend que gestiona los turnos (`turno-puerto/select`, `turno-puerto/actual`, `turno-puerto`). |
| **Calada** | Proceso de pesaje del camión al ingresar al puerto. La app permite registrar y rechazar caladas. |
| **Carta de porte** | Documento legal que acompaña el transporte de granos. La app permite escanear el código CTG (Código de Trazabilidad de Granos) y subir el PDF. |
| **CTG** | Código de Trazabilidad de Granos — código impreso en la carta de porte, escaneado con barcode scanner. |
| **Postulación** | Acción del chofer de aplicar a un pedido de carga publicado. |
| **Carga / Pedido** | Oferta de transporte de granos publicada por un operador. Los choferes la ven en la sección "Cargas". |
| **Viaje** | Estado activo de transporte. Un chofer tiene como máximo un viaje activo a la vez. |
| **Viaje-estado** | Cambio de estado del viaje (ej: en camino, en planta, cargado, descargado). |
| **Centro** | Centro de carga / descarga logístico. Identificado por CUIT (`33715514759` en el código de registro). |
| **Consulta** | Sistema de mensajería entre el chofer y el operador. Registra la última consulta activa. |
| **Novedad** | Noticia o comunicado publicado por Muvin a los choferes. Equivalente a "noticias". |
| **Siniestro** | Denuncia de accidente o incidente durante el viaje. |
| **Seguro / Póliza** | Documento de seguro del viaje, descargable como PDF desde el backend. |
| **Beneficio** | Beneficios o descuentos ofrecidos a choferes registrados. (BenefioPage — typo en clase) |
| **Compartir** | Función para invitar otros choferes enviando un SMS con el número de teléfono. |
| **CUIT/CUIL** | Código de identificación tributaria argentino. Usado como identificador del chofer y su empresa. |
| **BLoC** | Business Logic Component — patrón de arquitectura que separa UI de lógica mediante streams. |
| **BehaviorSubject** | Tipo de stream (RxDart) que emite el último valor almacenado a cualquier suscriptor nuevo. |
| **Preferences** | Clase singleton que encapsula SharedPreferences con ~30 campos tipados para persistencia local. |
| **Provider (InheritedWidget)** | Clase `Provider` que extiende `InheritedWidget` y expone los 13 BLoCs globalmente en el árbol de widgets. |
| **MuvinProvider** | Clase de servicio REST — no confundir con el Provider (InheritedWidget). |
| **FadePageRoute** | Clase personalizada de transición de página con animación de fade. |
| **Ventanilla** | Franja horaria de ingreso al puerto. Se selecciona al reservar un turno (campo `horario`). |
| **Turneada** | Flag persistido en SharedPreferences que indica si el viaje ya tiene turno asignado. |
| **xApiKey** | Header de autenticación adicional (`X-Api-Key`) enviado en todas las requests autenticadas. |
| **FCM** | Firebase Cloud Messaging — servicio de push notifications de Google/Firebase. |
| **serverToken** | Token privado de FCM hardcodeado en config, usado para enviar notificaciones desde la app (debería estar solo en el servidor). |
| **MovilKey** | Token FCM del dispositivo del chofer. Se registra en el backend via `chofer-app/set-movil-key`. |

---

Ver también: [[vision-general]] · [[arquitectura-alto-nivel]] · [[endpoints-rest]]
