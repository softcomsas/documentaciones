# Glosario — app-drivers

> [[vision-general]]

| Término | Definición |
|---------|------------|
| **Chofer** | Usuario de la app. Conductor de camión registrado en la plataforma Muvin. |
| **Carga** | Pedido público de transporte disponible para que los choferes se postulen. Equivale a `pedido-público` en el backend. |
| **Postulación** | Acción del chofer de ofrecerse para transportar una carga. |
| **Viaje** | Pedido asignado al chofer. Contiene origen, destino, producto, peso, etc. |
| **Remito inicial** | Foto del remito de carga que el chofer sube al iniciar el transporte. |
| **Remito final** | Foto del remito de descarga que el chofer sube al llegar al destino. |
| **Carta de porte** | Documento legal de transporte de granos (AFIP). La page `CartaPortePage` estaba destinada a gestionarla. |
| **Bearer token** | Token de autenticación obtenido tras la verificación de teléfono. Se almacena en `SharedPreferences` y se envía en el header `Authorization: Bearer <token>`. |
| **BLoC** | Business Logic Component — patrón de gestión de estado con Streams. Esta app usa una implementación manual con `InheritedWidget` + `RxDart`. |
| **SharedPreferences** | Almacenamiento clave-valor local de Android/iOS. Usado para persistir el token entre sesiones. |
| **Geolocator** | Plugin Flutter que accede al GPS del dispositivo. Retorna `Position` con latitud y longitud. |
| **MuvinProvider** | Clase Dart que encapsula todas las llamadas HTTP al backend. Equivalente a un Service/Repository. |
| **FutureBuilder** | Widget Flutter que reconstruye la UI cuando un `Future` cambia de estado (loading / data / error). Usado en todas las pages de datos. |
| **SwiperController** | Controlador del carrusel `flutter_swiper`. Usado en Cargas y Postulaciones para navegar entre tarjetas. |
| **InheritedWidget** | Mecanismo de Flutter para propagar estado por el árbol de widgets. Usado como contenedor del BLoC. |
| **pdf** | Campo en la respuesta de `mi-viaje`. Si `pdf == 1`, el chofer ya subió el remito inicial (habilita descarga); si `pdf == 0`, aún no lo subió (habilita carta de porte). |
