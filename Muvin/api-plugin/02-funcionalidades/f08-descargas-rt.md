# F-08 — DescargasRT: Descargas en Tiempo Real (COE)

## Descripción

Procesa **descargas de granos en tiempo real** identificadas por su **COE (Código de Operación Electrónica)**. A diferencia de los procesos de cupos, no trabaja con cartas porte sino con el estado de descargas registradas en AFIP. Introducido en 2025 mediante el módulo **PluginV2**.

## Clases principales

| Clase | Ruta |
|---|---|
| `DescargaRtProcess` | `source/components/PluginV2/procedures/DescargaRtProcess.php` |
| `BaseProcess` | `source/components/PluginV2/BaseProcess.php` |
| `DescargaRtParserFile` | `source/components/PluginV2/parsers/DescargaRtParserFile.php` |
| `MailClient` | `source/components/PluginV2/MailClient.php` |

## Activación

Activado vía `Ejecutor::correrProcesos()` cuando el `proceso.metodo` apunta a `DescargaRtProcess`.

La primera configuración conocida es para el cliente **Monsanto** (`m250203_135904_insert_rt_config_monsanto`).

## Flujo

```
BaseProcess::run()
    │
    ├─ MailClient::searchEmailsUnread() → lista de IDs
    │
    └─ foreach $email:
         │
         ├─ MailClient::getEmail($id) → EmailDto
         │      (asunto, remitente, adjuntos, fecha)
         │
         ├─ Filtrar: in_array($emailDto->subject, $this->subjectToIgnore())
         │
         └─ DescargaRtProcess::processEmail(EmailDto)
                │
                ├─ Obtener adjunto del EmailDto
                ├─ DescargaRtParserFile::parse($adjunto) → ResultParseDto
                │
                ├─ Si ResultParseDto tiene errores:
                │      BaseProcess::reportErrors() → email de notificación al operador
                │
                └─ Si OK:
                       DescargasRt::save([
                           'coe' => ...,
                           'grano' => ...,
                           'cosecha' => ...,
                           'cuitDepositario' => ...,
                           'nroPlanta' => ...,
                           'cuitDepositante' => ...,
                           'kilos' => ...,
                           'estado' => 'PENDIENTE'
                       ])
                       ServiciosDescargas::procesar($datos)
                       DescargasRt::update(estado = PROCESADO / ERROR)
                       DescargarRtNotif::registrar()
```

## Modelo de datos

**Tabla `descargas_rt`:**

| Campo | Descripción |
|---|---|
| `coe` | Código de Operación Electrónica (AFIP) — identificador único |
| `grano` | Tipo de grano |
| `cosecha` | Campaña agrícola |
| `cuitDepositario` | CUIT de la planta receptora |
| `nroPlanta` | Número de planta |
| `cuitDepositante` | CUIT del productor |
| `kilos` | Kilogramos |
| `estado` | PENDIENTE / PROCESADO / ERROR |

## Diferencias con procesos de cupos (v1)

| Aspecto | Cupos v1 | DescargasRT v2 |
|---|---|---|
| Identificador principal | nombre_archivo | COE (AFIP) |
| Arquitectura | Clases estáticas | Herencia + DTOs |
| IMAP client | Directo en la clase | Encapsulado en `MailClient` |
| Notificación de errores | Básica | `reportErrors()` estructurado |
| Tabla destino | `cupo_documento` | `descargas_rt` |

## Clientes conocidos

- **Monsanto** (configurado en migraciones de febrero/marzo 2025)

## Notificaciones

Si el parseo de un archivo RT falla, `BaseProcess::reportErrors()` envía un email al operador con:
- Errores generales del email (campo `GENERAL`)
- Errores por archivo adjunto (indexados por nombre de archivo)
- El asunto original del email para facilitar la identificación
