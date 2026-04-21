# Recomendaciones de Modernización

> **Contexto:** [[deuda-tecnica]] · [[hotspots]]

## Prioridad inmediata (< 1 semana)

| Acción | Esfuerzo | Impacto |
|--------|----------|---------|
| Fix `constant.ts` línea 17: `event.update` CMD | 1 línea | 🔴 Restaura funcionalidad de `event.update` |
| Agregar `.env.example` con las 4 variables requeridas | 30 min | Mejora DX para onboarding |
| Descommentar MS en `docker-compose.yml` | 30 min | Permite levantar el stack completo |

## Corto plazo (< 1 mes)

| Acción | Esfuerzo | Impacto |
|--------|----------|---------|
| Corregir unidad de `duration` en `LegacyService.update()` | 2h | Métricas correctas |
| Crear `DatabaseModule` global — eliminar PrismaService dual | 2h | Menos conexiones a MySQL |
| Reemplazar `void` por `await` en controllers | 1h | Errores visibles en logs |
| Agregar tests unitarios para `terms.ts` y `json.ts` | 4h | Red de seguridad mínima |
| Eliminar `IDENTITY` y `comprador-by-razon-social.ts` | 30 min | Limpieza de dead code |
| Remover `@nestjs/platform-express` de dependencies | 15 min | Dependencia innecesaria |

## Mediano plazo (1-3 meses)

### Tests de integración completos

Actualmente 0 tests. La cobertura mínima recomendada para producción es 80%.

Orden de implementación sugerido:
1. Tests de `LegacyService` (mayor complejidad y riesgo)
2. Tests de `MicroservicesService`
3. Tests E2E de message patterns

### Reemplazar errores silenciados

Migrar de `console.log(error)` a:
- `Logger.error()` de NestJS con contexto estructurado
- Integración con observabilidad externa (Sentry, Datadog, etc.)

### Búsqueda full-text real

El campo `search_terms` con `LIKE '%term%'` no escala. Alternativas:
- MySQL FULLTEXT INDEX (simple, sin infraestructura adicional)
- Elasticsearch / OpenSearch (para escala alta)
- Meilisearch (liviano, open source)

## Largo plazo (3+ meses)

### Separar `legacy_panel` y `legacy_descargas` en servicio propio

El módulo legacy tiene lógica muy específica que podría justificar su propio microservicio si el volumen crece significativamente.

### Considerar transporte NATS o RabbitMQ

TCP síncrono es simple pero no tiene:
- Persistencia de mensajes
- Reintentos automáticos
- Dead letter queues
- Backpressure

Si los callers críticos deben garantizar que el log fue procesado, un broker de mensajes es más robusto.

### Paginación en `searchTerms`

Actualmente `searchTerms` no tiene `take` ni paginación. Agregar límite de resultados para evitar queries sin límite.

---

*Ver también: [[deuda-tecnica]] · [[hotspots]] · [[security-inventory]]*
