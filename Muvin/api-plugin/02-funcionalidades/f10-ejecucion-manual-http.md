# F-10 — Ejecución Manual vía HTTP

## Descripción

El plugin expone endpoints HTTP que permiten disparar manualmente cualquier proceso sin necesidad de acceder al servidor por SSH. Útil para debugging, re-procesamiento de emails fallidos o ejecución puntual de un proceso específico.

## Controlador

`source/controllers/PluginController.php`  
**Base URL:** `/plugin`

## Endpoints disponibles

| Endpoint | Proceso |
|---|---|
| `GET /plugin` | Ejecuta todos los procesos activos (equivalente a `yii lector`) |
| `GET /plugin/proceso-aca` | Solo ACA |
| `GET /plugin/procesar-bunge` | Solo Bunge |
| `GET /plugin/procesar-samsa` | Solo SAMSA |
| `GET /plugin/procesar-cofco` | Solo Cofco |
| `GET /plugin/procesar-molinos-agro` | Solo MolinosAgro |
| `GET /plugin/procesar-dreyfus` | Solo Dreyfus |
| `GET /plugin/procesar-cofco-tomas-hnos` | Solo Cofco para Tomas Hnos |
| `GET /plugin/proceso-tomas-hnos-dreyfus` | Solo Dreyfus para Tomas Hnos |
| `GET /plugin/proceso-fyo` | Solo Fyo/Tomas Hnos |
| `GET /plugin/cargar-productos` | Sincroniza catálogo productos |
| `GET /plugin/cargar-destinos` | Sincroniza catálogo destinos |
| `GET /plugin/cargar-onccas-productos` | Sincroniza catálogo ONCCA |

## Respuesta

Todos los endpoints retornan JSON:

```json
// Éxito — array con resultados por configuración
[
  { "cliente": "Empresa X", "procesados": 2, "errores": 0 },
  { "cliente": "Empresa Y", "procesados": 0, "errores": 1 }
]

// Error — HTTP 404 con mensaje
"No se encontró el proceso"
```

## Caso de uso: re-procesamiento

Si un proceso falló en el cron y los emails ya fueron marcados como leídos, se puede usar estos endpoints junto con herramientas IMAP para volver a marcar los emails como no leídos y re-disparar el proceso.

## ⚠️ Advertencia de seguridad

Los endpoints **no requieren autenticación**. Cualquier persona con acceso a la red puede:
- Disparar el procesamiento de todos los clientes
- Sincronizar catálogos
- Potencialmente causar procesamiento duplicado de cupos

Se recomienda proteger estos endpoints con autenticación por token o restricción de IP. Ver [mejoras-propuestas.md](../08-riesgos-y-deuda-tecnica/mejoras-propuestas.md) MEJ-04.
