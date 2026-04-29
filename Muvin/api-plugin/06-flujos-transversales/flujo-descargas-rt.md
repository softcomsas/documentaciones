# Flujo: DescargasRT (PluginV2)

## Descripción

Flujo del módulo PluginV2 para procesamiento de **Descargas en Tiempo Real**. A diferencia del flujo de cupos, identifica las descargas por su COE (Código de Operación Electrónica de AFIP). Introducido en 2025.

## Diagrama de secuencia

```
cron (*/15 min) — mismo ciclo que yii lector
    │
    ▼
Ejecutor::correrProcesos()
    │
    └─ proceso.metodo = "DescargaRtProcess::run" (o similar)
         │
         ▼
    DescargaRtProcess extends BaseProcess
         │
         ▼
    BaseProcess::run()
         │
         ├─ MailClient::searchEmailsUnread()
         │
         └─ foreach $email:
              │
              ├─ MailClient::getEmail($id) → EmailDto
              │
              ├─ Filtrar por subject (subjectToIgnore())
              │
              ├─ DescargaRtProcess::processEmail(EmailDto)
              │      │
              │      ├─ Obtener adjunto del EmailDto
              │      │
              │      ├─ DescargaRtParserFile::parse($adjunto) → ResultParseDto
              │      │
              │      ├─ Si hay errores:
              │      │      BaseProcess::reportErrors() → Email de notificación
              │      │
              │      └─ Si OK:
              │             DescargasRt::save() (estado=PENDIENTE)
              │             ServiciosDescargas::procesar($coe, $datos)
              │             DescargasRt actualizar (estado=PROCESADO/ERROR)
              │             DescargarRtNotif::registrar()
              │
              └─ MailClient::markAsRead($email)
```

## Diferencias con flujo de cupos (v1)

| Aspecto | Cupos (v1) | DescargasRT (v2) |
|---|---|---|
| Identificador | nombre_archivo | COE (AFIP) |
| Estructura código | Clases estáticas | Clases con herencia |
| DTOs | No | EmailDto, ResultParseDto |
| IMAP client | Directo en clase | Encapsulado en MailClient |
| Tabla destino | `cupo_documento` | `descargas_rt` |
| Notificaciones | `cupo_mail_notif` | `descargas_rt_notif` |
| Introducido | 2021 | 2025 |

## Parseo de archivos RT

`DescargaRtParserFile` es responsable de:
1. Leer el archivo adjunto del email RT
2. Extraer los campos: COE, grano, cosecha, CUITs, nroPlanta, kilos
3. Retornar `ResultParseDto` con los datos o errores

## Notificación de errores

Si el parseo falla, `BaseProcess::reportErrors()` envía un email con:
- Los errores generales del email
- Los errores por archivo (si hay múltiples adjuntos)
- El asunto original del email para referencia

El destinatario del error se obtiene del template configurado en `email_template`.
