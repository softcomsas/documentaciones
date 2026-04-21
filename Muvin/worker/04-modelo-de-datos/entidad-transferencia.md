# Entidad: ITransferencia

> **Tipo:** Interface TypeScript
> **Archivo:** `src/modules/email/functions/rt.ts`
> **Módulo:** [[modulo-email]]
> **Rol:** Datos estructurados extraídos de un certificado de transferencia de depósito de granos (SENASA/AFIP)

---

## Descripción

Representa los datos del certificado de transferencia de depósito de granos del dominio agroindustrial argentino. Es el resultado del proceso de extracción y validación ejecutado por `extractAndValidateTextFn()` sobre el texto plano del PDF.

> [!warning] Sin persistencia actual
> A pesar de ser el objeto de negocio central del sistema, actualmente `ITransferencia` solo se envía a `console.log()` al final de `handleMail()`. No se persiste en base de datos ni se publica en otra cola.

---

## Definición TypeScript

```typescript
interface ITransferencia {
  fechaEmision: string;
  cosecha: string;
  cuitDepositario: string;
  cuitDepositante: string;
  cuitReceptor: string;
  datosAdicionales?: string;     // campo opcional
  coe: number;
  nroCertificado: number;
  nroPlanta: number;
  codigoGrano: number;
  kilos: number;
}

type TTransferencia = Required<ITransferencia>;
```

---

## Descripción de campos

| Campo | Tipo TS | Descripción | Restricciones de validación | Regex |
|-------|---------|-------------|---------------------------|-------|
| `coe` | `number` | Código Único de Operación Electrónica (COE) | Solo dígitos; máximo 12 dígitos | `/C\.O\.E\.\:\s*(\d+)/i` |
| `fechaEmision` | `string` | Fecha de emisión del certificado | Formato `YYYY-MM-DD`; ≥ 2010-01-01; ≤ hoy | `/Fecha\s+Emisi[oó]n\:\s*(\d{2}\/\d{2}\/\d{4})/i` |
| `cosecha` | `string` | Código de campaña agrícola | Solo: `2324`, `2425`, `2526`, `2627`, `2728` ⚠️ | `/Campa[ñn]a\:\s*(\d+)/i` |
| `cuitDepositario` | `string` | CUIT de quien deposita el grano físicamente | 11 dígitos; primer CUIT en el documento | matchAll global |
| `cuitDepositante` | `string` | CUIT del propietario del grano | 11 dígitos; segundo CUIT en el documento | matchAll global |
| `cuitReceptor` | `string` | CUIT de quien recibe la transferencia | 11 dígitos; tercer CUIT en el documento | matchAll global |
| `datosAdicionales` | `string?` | Observaciones opcionales del certificado | Máximo 255 caracteres; opcional | `/Datos\s+Adicionales\:\s*(.+?)/i` |
| `nroCertificado` | `number` | Número del certificado de transferencia | Solo dígitos; máximo 12 | `/DETALLE\s+DE\s+TRANSFERENCIA.*?\n\s*(\d+)/i` |
| `nroPlanta` | `number` | Número de la planta de almacenamiento | Solo dígitos; máximo 8 | `/N[°º]\s*de\s+Planta\:\s*(\d+)/i` |
| `codigoGrano` | `number` | ID del grano en el sistema | Solo `1` (TRIGO PAN) o `2` (SOJA) ⚠️ | `/Grano\s+y\s+Tipo\:\s*([A-ZÁÉÍÓÚÑ\s]+?)/i` → lookup |
| `kilos` | `number` | Kilogramos transferidos | Decimal ≤ 2 decimales; positivo | `/(\d+(?:\.\d+)?)\s*Kg/i` |

---

## Catálogo de granos hardcodeado

El campo `codigoGrano` se resuelve contra este array fijo en `rt.ts`:

```typescript
const grains = [
  { id: 1, name: 'TRIGO PAN' },
  { id: 2, name: 'SOJA' },
]
```

> [!danger] Catálogo incompleto
> La Argentina produce 14+ cultivos cubiertos por el sistema SENASA (maíz, girasol, sorgo, cebada, trigo candeal, etc.). El sistema actualmente **rechazará** cualquier certificado de un cultivo que no sea TRIGO PAN o SOJA con un error de validación. Esto es una limitación crítica de negocio.

---

## Catálogo de cosechas hardcodeado

El campo `cosecha` se valida contra estos valores fijos:

```typescript
const validCosechas = ['2324', '2425', '2526', '2627', '2728']
```

> [!danger] Time bomb 2028
> Los valores de cosecha tienen vencimiento. La campaña `2829` (aproximadamente 2028-2029) causará que el sistema rechace **todos** los certificados con error de validación, sin ningún warning previo en el código.

---

## Ejemplo de objeto ITransferencia

```json
{
  "coe": 230400012345,
  "fechaEmision": "2024-03-15",
  "cosecha": "2324",
  "cuitDepositario": "30-50000001-4",
  "cuitDepositante": "20-30000001-8",
  "cuitReceptor": "30-70000001-2",
  "datosAdicionales": "Traslado entre plantas",
  "nroCertificado": 1234567,
  "nroPlanta": 8765,
  "codigoGrano": 2,
  "kilos": 98450.50
}
```

---

## Relaciones

- **Producida por:** `extractAndValidateTextFn()` en [[email-parseo-certificado]]
- **Consumida por:** `console.log()` en `EmailProcessor.handleMail()` — 🔴 sin persistencia real

---

## Glosario de dominio

| Término | Descripción |
|---------|-------------|
| COE | Código Único de Operación Electrónica, asignado por AFIP a cada transferencia |
| Depositario | Empresa/persona que guarda físicamente el grano |
| Depositante | Propietario del grano depositado |
| Receptor | Quien recibe la propiedad del grano en la transferencia |
| Cosecha | Código de campaña agrícola, ej: `2324` = campaña 2023/2024 |
| CUIT | Clave Única de Identificación Tributaria argentina (11 dígitos) |

Ver [[glosario]] para términos adicionales.
