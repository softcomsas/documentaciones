# Deuda Técnica y Riesgos — muvin-api

> **Última revisión:** 2026-04-29

---

## Resumen

muvin-api tiene una base técnica moderna (NestJS 11, TypeScript estricto, arquitectura modular) pero presenta **puntos críticos de seguridad** y deuda de calidad que deben atenderse.

---

## 🔴 Crítico

| ID | Ítem | Ubicación | Descripción |
|----|------|-----------|-------------|
| DT-01 | Credenciales hardcodeadas | `temporary/configuration.ts` | `USER = '30556991835'`, `PASSWORD = 'Muvin2021*'` en texto plano en repositorio |
| DT-02 | `debug: true` en GraphQL | `modules/graphql.ts` | Puede exponer stack traces en respuestas GraphQL en producción |
| DT-03 | Sin caché de token en TemporaryModule | `temporary/service.ts` | 2 HTTP requests por cada llamada a descargas — y el token no se reutiliza |
| DT-04 | Sin timeout en HttpModule | `temporary/module.ts` | `timeout: 0` — un request puede colgar indefinidamente |

---

## 🟡 Alto

| ID | Ítem | Ubicación | Descripción |
|----|------|-----------|-------------|
| DT-05 | Errores silenciosos en logs | `logs/controller.ts` | `catch` captura errores y retorna `undefined` — fallos en ms-logs no son visibles al cliente |
| DT-06 | Sin tests escritos | Proyecto entero | Infraestructura Jest configurada pero sin tests |
| DT-07 | Sin Swagger/OpenAPI | Proyecto entero | No hay documentación automática de la API REST |
| DT-08 | `AROUND = 'panel'` fijo | `temporary/configuration.ts` | No se puede cambiar entorno sin recompilar; ignora variables de entorno |
| DT-09 | `body: unknown` en descargas | `temporary/controller.ts` | Sin validación del payload entrante |
| DT-10 | Módulo "Temporary" en producción | `temporary/` | Un módulo marcado como temporal lleva tiempo en producción sin plan de migración |

---

## 🟢 Bajo

| ID | Ítem | Descripción |
|----|------|-------------|
| DT-11 | `// eslint-disable-next-line` en todos los módulos | Módulos vacíos requieren `no-extraneous-class` deshabilitado; podría resolverse con patrón diferente |
| DT-12 | Sin healthcheck endpoint | No hay `/health` o `/ping` para monitoreo/orquestación |
| DT-13 | Límite de payload 50 MB | Aumentado sin comentario explicando el caso de uso; puede ser un vector de DoS |

---

## Plan de mejora sugerido

### Inmediato

1. **Mover credenciales** de `configuration.ts` a variables de entorno:
   ```typescript
   USER:     process.env.DESCARGAS_USER,
   PASSWORD: process.env.DESCARGAS_PASSWORD,
   ```
2. **Desactivar `debug: true`** en GraphQL para producción (usar variable de entorno)
3. **Agregar timeout** al `HttpModule` del módulo temporary (ej: 30 segundos)

### Corto plazo

4. **Cachear el token** de descargas-app con TTL (en memoria o Redis)
5. **Agregar Swagger** (`@nestjs/swagger`) para documentar los endpoints REST
6. **Escribir tests unitarios** para los 3 servicios (LogsService, TemporaryService, LegacyResolver)

### Mediano plazo

7. **Eliminar/migrar TemporaryModule** a un microservicio dedicado de descargas
8. **Agregar endpoint `/health`** con estado de los microservicios conectados
9. **Implementar retry logic** para las llamadas a microservicios

---

## Referencias

- [[stack-tecnologico]]
- [[modulo-temporary]]
- [[modulo-logs]]
- [[arquitectura-general]]
