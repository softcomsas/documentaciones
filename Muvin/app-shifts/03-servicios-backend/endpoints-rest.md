# Endpoints REST — app-shifts

Base URL: `https://panel.muvinapp.com/api/backend/web/`

## Headers

```
# Requests autenticadas
Authorization: Bearer {accessToken}
X-Api-Key: uH9hatw8G3lQls7fE3

# Requests con JSON body
Content-Type: application/json; charset=UTF-8
```

## Autenticación

| Método | Endpoint | Body | Autenticado | Descripción |
|--------|----------|------|-------------|-------------|
| GET | `chofer-app/telefono?telefono={tel}` | — | No | Verifica existencia de teléfono |
| POST | `chofer-app/login` | `{telefono, codigo}` | No | Login con código SMS |
| POST | `site/register-phone` | `{phone}` | No | Registra teléfono nuevo |
| POST | `chofer-app/solicitar-recuperarcion` | `{cuit}` | No | Solicita recuperación de cuenta |
| POST | `chofer-app/recuperar-cuenta` | `{codigo, cuit, telefono}` | No | Recupera cuenta con código |
| GET | `chofer-app/validar-cuit?cuit={cuit}` | — | No | Valida CUIT argentino |

## Perfil del chofer

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| POST | `chofer-app/registrar` | JSON persona + camion + acoplado | Registra nuevo chofer |
| POST | `chofer-app/actualizar` | JSON persona + camion + acoplado | Actualiza datos del chofer |
| GET | `chofer-app/set-movil-key?movil_key={token}` | — | Registra token FCM del dispositivo |
| GET | `chofer-app/actualizar-posicion` | `{lat, lng, ...}` | Actualiza posición GPS del chofer |
| GET | `chofer/quitar-no-disponible` | — | Activa al chofer (disponible) |
| GET | `chofer/poner-no-disponible` | — | Desactiva al chofer (no disponible) |

### Estructura JSON para registrar/actualizar chofer

```json
{
  "persona": {
    "cuit_cuil": "...",
    "nombre": "...",
    "apellidos": "...",
    "razon_social": "apellidos,nombre",
    "id_localidad": "...",
    "domicilio": "...",
    "password": "P@ssW{cuit}",
    "telefono": "...",
    "email": "...",
    "app_chofer_instalada": 1
  },
  "camion": {
    "id_marca_camion": "...",
    "id_tipo_camion": "...",
    "patente": "..."
  },
  "acoplado": {
    "id_marca_acoplado": "...",
    "id_tipo_acoplado": "...",
    "patente": "..."
  },
  "centro": "33715514759"
}
```

> ⚠️ La contraseña se genera automáticamente como `P@ssW{cuit}` — sin intervención del usuario.

## Cargas y postulaciones

| Método | Endpoint | Body/Params | Descripción |
|--------|----------|-------------|-------------|
| GET | `chofer/pedidos-publicos?{filtros}` | Query params: centro, localidad_carga, da_efectivo, da_gasoil | Lista cargas disponibles con filtros |
| GET | `chofer-postulado` | — | Lista postulaciones del chofer |
| POST | `chofer-postulado` | `{id_pedido}` | Postularse a una carga |
| DELETE | `chofer-postulado/{id}` | — | Cancelar postulación |

## Viaje

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| GET | `viaje/viaje-chofer` | — | Obtiene el viaje activo del chofer |
| PUT | `viaje/{idViaje}` | `{...datos}` | Actualiza datos del viaje |
| POST | `viaje-estado` | `{...myData}` | Cambia estado del viaje |
| POST | `viaje/pdf-up?id={idViaje}` | multipart file | Sube PDF de carta de porte |
| GET | `viaje/pdf-seguro-down?id={idViaje}` | — | Descarga póliza de seguro (PDF) |
| GET | `busqueda/sin-notificar` | — | Obtiene propuesta de viaje pendiente |
| PUT | `busqueda-chofer/respuesta?id={idBusqueda}` | `{interesado: bool}` | Acepta/rechaza propuesta de viaje |

## Turnos de puerto

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| POST | `turno-puerto/actual` | `{puerto, id_chofer, ...}` | Obtiene turno activo del chofer |
| POST | `turno-puerto/select` | `{puerto, producto, fecha, inicio, fin}` | Lista ventanillas disponibles |
| POST | `turno-puerto` | `{...datos del turno}` | Confirma reserva de turno |
| PUT | `turno-puerto/{id}` | `{...nuevos datos}` | Reprograma turno existente |

## Calada

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| POST | `calada-rechazada` | `{...myData}` | Registra rechazo de calada |

## Consultas

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| GET | `consulta/ultima-chofer` | — | Obtiene la última consulta activa |
| POST | `consulta` | `{...myData}` | Envía nueva consulta al operador |

## Novedades

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `noticias` | Lista todas las novedades |
| GET | `noticias?search[id]={id}` | Obtiene detalle de una novedad |

## Seguros y siniestros

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| POST | `siniestro` | `{...datos}` | Denuncia un siniestro |

## Desvíos

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| POST | `desvio` | `{...myData}` | Registra un desvío en el viaje |

## Nomencladores / Selects

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `select?expand={nomenclador}` | Obtiene datos de un nomenclador expandido |
| GET | `{url}` | Obtiene nomenclador por URL arbitraria |
| GET | `select/provincias?id_pais={pais}` | Lista provincias por país |
| GET | `select/localidades?id_provincia={prov}` | Lista localidades por provincia |

## Mensajería / Compartir

| Método | Endpoint | Body | Descripción |
|--------|----------|------|-------------|
| POST | `messenger/sms` | `{number, message}` | Envía SMS a otro teléfono (compartir app) |

## Socket.IO

URL: `https://sockets.muvinapp.com`

| Evento | Dirección | Datos | Descripción |
|--------|-----------|-------|-------------|
| `connect` | servidor → cliente | — | Conexión establecida |
| `configurar-usuario` | cliente → servidor | `{nombre, id_chofer}` | Registra al chofer en el socket |
| `respuesta-consulta` | servidor → cliente | `{...}` | Recibe respuesta a consulta del operador |

---

Ver también: [[arquitectura-alto-nivel]] · [[push-notifications]] · [[socket]]
