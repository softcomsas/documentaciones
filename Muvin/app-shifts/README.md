# app-shifts

Aplicación móvil Flutter para choferes de la plataforma Muvin. Permite gestionar **turnos de puerto**, viajes, cargas, postulaciones, caladas, seguros y comunicación en tiempo real vía Socket.IO.

## Índice de documentación

| Sección | Descripción |
|---------|-------------|
| [00 · Visión general](./00-overview/vision-general.md) | Qué es, para qué sirve, contexto de negocio |
| [01 · Módulos](./01-modulos/index.md) | Páginas, BLoCs y providers |
| [02 · Funcionalidades](./02-funcionalidades/index.md) | Flujos funcionales detallados |
| [03 · Servicios backend](./03-servicios-backend/endpoints-rest.md) | Todos los endpoints REST y Socket.IO |
| [04 · Modelo de datos](./04-modelo-de-datos/chofer-model.md) | Modelos tipados y SharedPreferences |
| [05 · Inventario](./05-inventarios/inventario-general.md) | Árbol de archivos, dependencias, credenciales |
| [06 · Flujos transversales](./06-flujos-transversales/arranque.md) | Inicio de app, autenticación, turno, GPS |
| [07 · Operación y despliegue](./07-operacion-despliegue/build-flutter.md) | Build, Firebase, variables de entorno |
| [08 · Riesgos y deuda técnica](./08-riesgos-deuda-tecnica/riesgos.md) | FCM token expuesto, Flutter 1.x EOL, deps obsoletas |

## Stack rápido

- **Flutter 1.x / Dart ≥2.1.0 <3.0.0** — sin null safety, EOL
- **API REST**: `https://panel.muvinapp.com/api/backend/web/` (PRODUCCIÓN)
- **Socket.IO**: `https://sockets.muvinapp.com`
- **Firebase**: Messaging + Analytics
- **Mapas**: MapBox SDK
- **Patrón de estado**: BLoC manual + RxDart + InheritedWidget

## Advertencia de seguridad

> 🔴 **CRÍTICO:** El repositorio contiene 4 credenciales hardcodeadas en `lib/src/config/config.dart`: FCM server token, MapBox token, xApiKey y la URL de API de producción. Ver [08-riesgos](./08-riesgos-deuda-tecnica/riesgos.md).
