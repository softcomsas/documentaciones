# Módulo: Common / Utilitarios

> **Ruta/Namespace:** `src/common/`
> **Criticidad:** 🟢 Baja
> **Estado:** Activo (con código muerto)

## Propósito

Agrupa utilidades compartidas del microservicio: funciones de uso general, interfaces genéricas y tipos auxiliares. En su estado actual contiene tanto código activamente usado como código muerto que no tiene referencias en el proyecto.

## Componentes activos

| Componente | Archivo | Propósito |
|------------|---------|-----------|
| `LOG` | `functions/logger.ts` | Wrapper de NestJS Logger con resaltado ANSI de contexto `[TAG]` |
| `IDENTITY` | `functions/identity.ts` | Función identidad `<T>(x: T): T => x` para adapters sin transformación |

## Componentes sin uso detectado (candidatos a eliminación)

| Componente | Archivo | Razón |
|------------|---------|-------|
| `IOption<T>` | `interfaces/option.ts` | Sin referencias en el codebase |
| `IOptionExtended<T>` | `interfaces/option-extended.ts` | Sin referencias en el codebase |
| `TGraphQlOperation` | `types/graphql-operation.ts` | No hay GraphQL en el proyecto |
| `THttpMethod` | `types/http-method.ts` | Duplica `TMethod` en `src/types/methods.ts` |
| `TStatus` | `types/status.ts` | Sin referencias visibles |

## Funcionamiento de `LOG`

El logger detecta si el string tiene un patrón `[TAG]` al inicio y aplica colores ANSI:
- El `[TAG]` aparece en amarillo.
- El resto del mensaje aparece en verde.
- Si se pasa un segundo argumento `data`, lo imprime con `console.log(data)` (sin sanitización).

```
LOG('[AppService] Procesando solicitud', { endpoint: 'comprador-by-razon-social' });
// → [AppService] en amarillo, "Procesando solicitud" en verde
// → console.log del objeto data
```

## Riesgos y deuda técnica detectados

- 💀 **4 exports muertos:** `IOption`, `IOptionExtended`, `TGraphQlOperation`, `THttpMethod` deben eliminarse.
- ⚠️ **`TStatus`:** `'PENDING' | 'SUCCESS' | 'ERROR' | 'TIMEOUT'` — parece un artefacto de una versión anterior. Sin uso detectado.
- ⚠️ **`LOG` usa `console.log` para el campo `data`:** no pasa por NestJS Logger. Si el nivel de logs está configurado para suprimir ciertos niveles, `data` siempre se imprimirá igual.

## Archivos fuente relevantes

- `src/common/_index.ts`
- `src/common/functions/logger.ts`
- `src/common/functions/identity.ts`
- `src/common/interfaces/option.ts` (💀)
- `src/common/interfaces/option-extended.ts` (💀)
- `src/common/types/graphql-operation.ts` (💀)
- `src/common/types/http-method.ts` (💀)
- `src/common/types/status.ts` (⚠️ sin uso visible)
