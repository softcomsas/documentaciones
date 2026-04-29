# Deuda Técnica

## DT-01: Coexistencia de dos estilos arquitectónicos

**Descripción:** El proyecto tiene dos módulos con arquitecturas incompatibles:
- `Plugin/Cupos/` (v1): clases estáticas, sin DTOs, IMAP directo en cada clase
- `PluginV2/` (v2): herencia, DTOs, MailClient encapsulado

**Impacto:** Dificulta el mantenimiento. Un developer nuevo debe aprender dos estilos.

**Solución:** Migrar gradualmente los procesos de v1 a la arquitectura de v2:
1. Crear `ProcesoAcaV2 extends BaseProcess`
2. Crear `AcaParserFile extends` (interfaz de parser)
3. Eliminar `ProcesoAca.php`
4. Repetir para cada empresa

**Esfuerzo estimado:** Alto (9 procesos × ~1 día = ~2 semanas)

---

## DT-02: Alta de clientes mediante migraciones de BD

**Descripción:** Agregar un nuevo cliente requiere crear y ejecutar 5 archivos de migración PHP manualmente. No hay panel de administración para gestionar clientes.

**Evidencia:** Las migraciones de 2023 muestran docenas de alta de clientes: `m230411_174136_cliente_add`, `m230811_172302_cliente_add`, etc.

**Impacto:** Proceso de onboarding lento y propenso a errores. Requiere acceso al servidor de producción para ejecutar migraciones.

**Solución:** Construir un panel de administración CRUD (o endpoints API) para gestionar:
- Clientes
- Procesos
- Configuraciones IMAP
- Templates de email

---

## DT-03: Parsers frágiles por empresa

**Descripción:** Cada `LeerHtml*.php` contiene lógica de parseo frágil (regex sobre HTML, coordenadas en PDF) que puede romperse si la empresa cambia el formato de sus notificaciones.

**Impacto:** Cada cambio de formato de una cerealera requiere código nuevo y un deploy.

**Solución:** 
- Añadir tests para cada parser con fixtures de emails reales (anonimizados)
- Considerar parsers más resilientes (extraer por labels en lugar de posición)

---

## DT-04: Sin tests automatizados visibles

**Descripción:** Hay archivos `TestController.php` y `TestExcelController.php` pero son comandos de consola manuales. El directorio `tests/` existe pero no está evidente que haya tests unitarios o de integración funcionando.

**Impacto:** Sin tests, cualquier cambio puede romper el sistema en producción sin que se detecte antes del deploy.

**Solución:** Implementar tests con Codeception (ya configurado vía `codeception.yml`):
- Tests unitarios para los parsers (LeerPdf, LeerHtml*)
- Tests de integración para el flujo completo con mocks de IMAP y Muvin API

---

## DT-05: Archivos legacy en el repositorio

**Descripción:** El repositorio contiene archivos que no deberían estar en producción:
- `Email.php.bak` — backup de un componente
- `Vagrantfile` — entorno de desarrollo obsoleto
- `.bowerrc` — Bower para assets frontend (no hay frontend)
- `Base de datos/Tablas.sql` — script SQL posiblemente desactualizado

**Solución:** Limpiar el repositorio:
```bash
git rm source/components/Email.php.bak
git rm source/Vagrantfile
git rm source/.bowerrc
# Evaluar si Tablas.sql está actualizado o eliminarlo
```

---

## DT-06: Sin control de concurrencia en el cron

**Descripción:** El cron ejecuta `yii lector` cada 15 minutos sin verificar si ya hay una ejecución en curso. Si una ejecución es lenta (ej: servidor IMAP lento), las siguientes se superponen.

**Impacto:** Procesamiento duplicado de emails → duplicación de cupos en Muvin.

**Solución:** Usar `flock` en el cron:
```bash
*/15 * * * * flock -n /tmp/plugin_lector.lock php /app/yii lector
```

---

## DT-07: Logging insuficiente

**Descripción:** El logging actual se basa en redirigir la salida estándar al cron.log. No hay logging estructurado ni métricas de:
- Cuántos cupos se procesaron por ciclo
- Tiempo de procesamiento por empresa
- Tasa de éxito/error por empresa

**Solución:** 
- Usar `Yii::info()` / `Yii::error()` consistentemente
- Considerar integración con un sistema de logging centralizado (ELK, Sentry)

---

## Resumen de deuda técnica

| ID | Descripción | Prioridad | Esfuerzo |
|---|---|---|---|
| DT-01 | Migrar Plugin/Cupos/ a PluginV2 | Media | Alto |
| DT-02 | Panel de administración de clientes | Alta | Alto |
| DT-03 | Parsers frágiles sin tests | Alta | Medio |
| DT-04 | Sin tests automatizados | Alta | Medio |
| DT-05 | Archivos legacy en repo | Baja | Bajo |
| DT-06 | Sin control de concurrencia | Media | Bajo |
| DT-07 | Logging insuficiente | Media | Bajo |
