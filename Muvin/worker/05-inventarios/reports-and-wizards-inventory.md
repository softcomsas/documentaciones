# Inventario de Reportes y Wizards

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Ver también:** [[functional-classification]], [[modulo-email]]

---

> [!info] Contexto
> Este proyecto es un **worker asíncrono**. No tiene interfaz de usuario, ni reportes tradicionales, ni wizards. Este inventario documenta los **outputs estructurados** que produce el sistema como equivalente funcional de los reportes.

---

## Outputs de datos estructurados

| # | Nombre | Propósito | Archivo origen | Datos producidos | Módulo | Estado |
|---|--------|-----------|---------------|-----------------|--------|--------|
| 1 | `ITransferencia` (extracción) | Datos del certificado de transferencia de depósito de granos parseados desde PDF | `src/modules/email/functions/rt.ts` | COE, fechaEmision, cosecha, cuitDepositario, cuitDepositante, cuitReceptor, nroCertificado, nroPlanta, codigoGrano, kilos, datosAdicionales | `email` | 🔴 **Activo pero sin destino** — resultado enviado a `console.log()` |
| 2 | `extractAndValidateTextFn` result | Objeto `{ success, data, errors }` con resultado de parsing | `src/modules/email/functions/rt.ts` | `success: boolean`, `data: TTransferencia \| null`, `errors: string[]` | `email` | 🔴 Solo se loguea, no se persiste ni reenvía |

---

## Procesos automáticos (equivalentes a batch reports)

| # | Nombre | Trigger | Frecuencia | Origen de datos | Destino | Estado |
|---|--------|---------|-----------|----------------|---------|--------|
| 1 | Procesamiento de certificado PDF | Job en cola Bull `email.pdf` | Evento (push de job desde API principal) | Gmail corporativo + adjunto PDF | ⚠️ `console.log()` — sin persistencia | 🔴 Incompleto |
| 2 | Notificación interna | Job en cola Bull `internal.notification` | Evento | ⚠️ Pendiente de verificar | ⚠️ Pendiente de verificar | 🚧 Sin implementar |

---

## Wizards / Asistentes

| Estado |
|--------|
| 💀 No aplica — el sistema no tiene interfaz de usuario |

---

> [!danger] Hallazgo crítico
> El output principal del worker (`ITransferencia` extraído del PDF) **no se persiste en ninguna base de datos ni se envía a ningún servicio**. El procesamiento termina en un `console.log(data, name)`. Esto sugiere que la integración con el API principal o la BD está **incompleta o fue eliminada**. Ver [[hotspots]] y [[deuda-tecnica]].
