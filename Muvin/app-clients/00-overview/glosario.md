# Glosario — App Clients

> **Última revisión:** 2026-04-30

## Términos de negocio

| Término | Definición |
|---------|-----------|
| **Cupo** | Unidad de capacidad de carga disponible en un centro/origen para una fecha dada. |
| **Dador de cupo** | Empresa o persona que pone cupos disponibles para que otros los soliciten. Flag `esDadorCupo == '1'`. |
| **Cliente final** | Usuario que solicita cupos para sus propias cargas. Flag `esClienteFinal == '1'`. |
| **Demandante** | Quien solicita/demanda un cupo. |
| **Destinatario** | Quien recibe la carga. Rol ID `7`. Flag `esDestino == '1'`. |
| **Solicitud** | Pedido de asignación de cupo realizado por un demandante. |
| **Asignar cupo** | Operación de vincular un cupo disponible a un demandante específico. |
| **Recuperar cupo** | Devolver un cupo asignado al pool disponible. |
| **Cabecera** | ⚠️ Pendiente de verificar. Entidad de agrupación de cupos (`v3/cabecera`). |
| **CCPP** | ⚠️ Pendiente de verificar. Posiblemente "carta de porte provisional" o "centro de carga/punto de partida". |
| **Carga solicitud distribuida** | Solicitud de cupo repartida entre múltiples orígenes o fechas. |
| **Pedido rápido** | Alta de pedido de carga con asignación directa de patente (`pedido/rapido`). |
| **Entregador** | Entidad que realiza la entrega física de la mercadería. |
| **Zona** | Agrupación geográfica de orígenes/destinos. |
| **Producto** | Tipo de grano o mercadería (soja, maíz, trigo, etc.). |
| **CUIT/CUIL** | Identificación tributaria argentina de empresas/personas. |
| **Términos y condiciones** | Acuerdo legal que el usuario debe aceptar al registrarse (`termino/actualizar`). |

## Términos técnicos

| Término | Definición |
|---------|-----------|
| **BLoC** | Business Logic Component. Patrón de arquitectura Flutter que separa la lógica de negocio de la UI usando Streams. |
| **RxDart** | Extensión de Dart Streams con operadores reactivos adicionales (usado para BLoC). |
| **Provider** | Clase propia (`blocs/provider.dart`) que inyecta los BLoCs en el árbol de widgets via `InheritedWidget`. No es el paquete `provider`. |
| **SharedPreferences** | Almacenamiento key-value persistente en el dispositivo. Usado para tokens y preferencias de sesión. |
| **access_token** | JWT de corta duración para autenticar peticiones HTTP. |
| **refresh_token** | Token de larga duración para renovar el access_token automáticamente. |
| **Socket.IO** | Librería de comunicación en tiempo real WebSocket. Conecta a `socket.muvinapp.com`. |
| **FCM** | Firebase Cloud Messaging. Sistema de push notifications. |
| **X-Api-Key** | Clave fija de API enviada en el header de cada petición. Hardcodeada en `Config`. |
| **Sink** | Canal de entrada de un Stream BLoC (para enviar eventos). |
| **Stream** | Canal de salida de un BLoC (para escuchar cambios de estado). |
