# Visión general — app-shifts

## ¿Qué es?

`app-shifts` (nombre de paquete: `muvin_app_choferes`) es la aplicación móvil Flutter destinada a los **choferes** de la plataforma **Muvin**. Su funcionalidad central —que le da el nombre al repositorio— es la **gestión de turnos de puerto** (`turno-puerto`): reserva, consulta y reprogramación de turnos en terminales portuarias o centros de carga de granos.

Es la versión más completa de la app de choferes: mientras `app-drivers` es una versión recortada, `app-shifts` incluye además turnos, calada, consultas, seguros, novedades y beneficios.

## Contexto de negocio

Muvin es una plataforma logística argentina para el transporte de granos y cargas. El chofer utiliza esta app para:

1. **Registrarse / iniciar sesión** vía teléfono + código SMS
2. **Ver cargas disponibles** con filtros (centro, localidad, efectivo, gasoil)
3. **Postularse** a pedidos y gestionar postulaciones
4. **Gestionar el viaje activo**: estado, carta de porte, calada, seguros
5. **Reservar y reprogramar turnos** de ingreso a puerto
6. **Comunicarse** vía consultas con operadores
7. **Mantenerse informado** con novedades/noticias
8. **Acceder a beneficios** disponibles para choferes
9. **Compartir la app** con otros choferes vía SMS

## Relación con otros sistemas

```
app-shifts  ──► api-backend (panel.muvinapp.com)  ──► Base de datos
            ──► api-sockets (sockets.muvinapp.com)  (tiempo real)
            ──► Firebase (push notifications + analytics)
            ──► MapBox (mapas y geolocalización)
            ──► FCM (envío de notificaciones push)
```

## Versión y estado

| Parámetro | Valor |
|-----------|-------|
| Versión | 1.0.0+1 |
| SDK Flutter | 1.x (obsoleto, EOL) |
| Dart SDK | ≥2.1.0 <3.0.0 (sin null safety) |
| Plataformas | Android (iOS no confirmado) |
| Idiomas | Español + inglés |
| Localización legacy | `language: 'vi'`, `region: 'VN'` (Vietnam, sin uso activo) |

## Diferencias clave respecto a app-drivers

| Característica | app-drivers | app-shifts |
|---------------|-------------|------------|
| Rutas | 11 | 28 |
| BLoCs | 3 | 13 |
| Turnos de puerto | ❌ | ✅ |
| Calada | ❌ | ✅ |
| Seguros | ❌ | ✅ |
| Consultas | ❌ | ✅ |
| Novedades | ❌ | ✅ |
| Beneficios | ❌ | ✅ |
| Socket.IO | ❌ | ✅ |
| Firebase Analytics | ❌ | ✅ |
| Barcode scanner | ❌ | ✅ |
| Generación PDF | ❌ | ✅ |
| API URL | Tests | **Producción** |

---

Ver también: [[arquitectura-alto-nivel]] · [[stack-tecnologico]] · [[glosario]]
