# Flujo: Descarga y Carga de Cupos

## Descripción

Flujo principal del plugin. Se ejecuta cada 15 minutos vía cron (`yii lector`). Para cada cliente activo, descarga emails de la cerealera configurada, parsea los adjuntos y carga los cupos en la plataforma Muvin.

## Diagrama de secuencia

```
cron (*/15 min)
    │
    ▼
LectorController::actionIndex()
    │
    ▼
Ejecutor::correrProcesos()
    │
    ├─ Cliente::find() WHERE activo=1
    │
    └─ foreach $cliente:
         │
         ├─ ClienteConfiguracion::find() WHERE cliente_id=X, activo=1
         │
         └─ foreach $clienteConfig:
              │
              ├─ Proceso::find() WHERE cliente_id=X, activo=1
              │
              └─ foreach $proceso:
                   │
                   ▼
               call_user_func($proceso->metodo, $clienteConfig)
                   │
                   ▼
               ProcesoXxx::DescargarMails($clienteConfig)
                   │
                   ├─ 1. Conectar IMAP
                   │      imap_open($clienteConfig->srvmail, $clienteConfig->usermail, $clienteConfig->pwdmail)
                   │
                   ├─ 2. Buscar emails no leídos
                   │      imap_search($inbox, 'UNSEEN')
                   │
                   └─ foreach $email:
                        │
                        ├─ 3. Descargar adjunto
                        │
                        ├─ 4. Parsear adjunto
                        │      LeerPdf::parsear($archivo)  (ACA)
                        │      LeerHtmlXxx::parsear($html) (otras empresas)
                        │
                        ├─ 5. Guardar CupoDocumento (estado=PENDIENTE)
                        │      CupoDocumento::save()
                        │
                        ├─ 6. Cargar en Muvin
                        │      ServiciosMuvin::login($cliente->user_muvin, $cliente->getPassword())
                        │      ServiciosMuvin::cargarCupo(new objCupoMuvin($cupoDoc))
                        │
                        ├─ 7. Actualizar estado
                        │      PROCESADO → si OK
                        │      ERROR → si falla
                        │
                        └─ 8. Marcar email como leído
                               imap_setflag_full($inbox, $msgno, '\\Seen')
```

## Detalle por paso

### Paso 1: Conexión IMAP

```php
$inbox = imap_open(
    '{' . $client_conf->srvmail . ':993/imap/ssl}INBOX',
    $client_conf->usermail,
    $client_conf->pwdmail
);
```

### Paso 4: Parseo según empresa

| Empresa | Parser | Formato |
|---|---|---|
| ACA | `LeerPdf::parsear()` | PDF — extrae texto con coordenadas |
| Bunge | `LeerHtmlBungue::parsear()` | HTML — DOMDocument/regex |
| Cofco | `LeerHtmlCofco::parsear()` | HTML |
| Dreyfus | HTML con regex | HTML |
| SAMSA | `LeerHtmlSamsa::parsear()` | HTML |
| MolinosAgro | `LeerHtmlMolinosAgro::parsear()` | HTML |
| Fyo | `LeerHtmlTomasHnosFyo::parsear()` | HTML |

### Paso 6: Payload hacia Muvin API

```php
$cupoMuvin = new objCupoMuvin($cupoDocumento);
// Mapea: titular, rem_com_productor, destinatario, destino, grano_especie, cosecha, códigos CP
$servicio->cargarCupo($cupoMuvin);
```

## Condiciones de error

| Condición | Comportamiento |
|---|---|
| IMAP no accesible | Excepción capturada, log de error |
| Email sin adjunto esperado | Se ignora el email |
| Adjunto no parseable | CupoDocumento con estado ERROR |
| Muvin API no responde | CupoDocumento con estado ERROR |
| Muvin API rechaza el cupo | CupoDocumento con estado ERROR |
| Proceso no existe o inactivo | HTTP 404 (endpoint) / skip (cron) |

## Idempotencia

> ⚠️ **Riesgo**: Los emails se marcan como leídos DESPUÉS de cargar en Muvin. Si el proceso falla a mitad, el email puede procesarse nuevamente en el siguiente ciclo, generando duplicados en Muvin.

La tabla `cupo_documento` puede usarse para detectar duplicados por `nombre_archivo`, pero no hay una verificación explícita visible antes de insertar.
