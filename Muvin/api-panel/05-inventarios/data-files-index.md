# Índice de Archivos de Datos — Muvinapp

> **Última revisión:** 2026-04-21
> **Ver también:** [[tree-estructura-archivos]], [[stack-tecnologico]]

---

## Scripts SQL fuera del sistema de migraciones

| Archivo | Ruta | Propósito | Formato | ¿Se regenera? | Riesgo |
|---------|------|-----------|---------|---------------|--------|
| `cuit_empresa.sql` | `script/cuit_empresa.sql` | Carga inicial/actualización de CUITs de empresas | SQL | Manual | ⚠️ No trazable — no es migración |
| `script limpia centro.sql` | `script/script limpia centro.sql` | Limpieza de datos de centros | SQL | Manual | ⚠️ Destructivo — debe ejecutarse con cuidado |
| `validaciones.sql` | `script/validaciones.sql` | Consultas de validación de datos | SQL | Manual | 🟡 Solo lectura |
| `log.cronAfip.txt` | `console/log.cronAfip.txt` | Log del cron job AFIP | TXT | 🔴 Commiteado en repo | 🔴 Expone logs de producción |

---

## Migraciones de base de datos

| Estadística | Valor |
|-------------|-------|
| Total migraciones | **623** |
| Ruta | `console/migrations/` |
| Framework | Yii2 migrations |
| Nomenclatura | `m<YYMMDD>_<HHMMSS>_<descripcion>.php` |

### Migraciones recientes (últimas conocidas)

| Migración | Descripción |
|-----------|-------------|
| `m260113_133514_add_column_created_at.php` | Añade columna `created_at` |
| `m251105_133514_update_rechazados_anulados_demanda_cupo.php` | Actualiza rechazados/anulados en demanda cupo |
| `m251104_135203_change_fecha_columns_in_carta_porte_table.php` | Modifica columnas de fechas en carta de porte |
| `m251030_133514_add_column_rechazados_demanda_cupo.php` | Añade columna de rechazados |
| `m251029_133513_create_config_vendedor_constantes.php` | Crea config para constantes de vendedor |
| `m251025_165915_rename_updateAt_to_updatedAt_in_intervinientes.php` | Renombra columna |
| `m251003_182800_add_rol_vendedor.php` | Agrega rol vendedor |
| `m251003_124657_queuesize.php` | Config queue size |
| `m251003_124554_priority.php` | Config prioridad queue |
| `m251003_124459_retry.php` | Config reintentos queue |
| `m251003_124355_later.php` | Config demora queue |
| `m251003_122931_queue.php` | Crea tabla queue |
| `m250930_203654_create_cartaPorte_interviniente_table.php` | Tabla carta porte + intervinientes |
| `m250930_203342_create_tipo_interviniente_table.php` | Tabla tipos de interviniente |
| `m250804_183824_change_latlong_to_double.php` | Coordenadas a DOUBLE |
| `m250708_142227_insert_comprador_default.php` | Rol comprador default |
| `m250708_142225_create_config_constantes.php` | Config constantes |
| `m250708_142224_add_rol_comprador.php` | Agrega rol comprador |
| `m250708_142223_create_cupo_comprador_table.php` | Tabla cupo-comprador |
| `m250225_135440_add_new_column_to_configuracion_centro.php` | Nueva columna configuración centro |

---

## Archivos de configuración relevantes

| Archivo | Ruta | Propósito | ¿En repo? | Riesgo |
|---------|------|-----------|-----------|--------|
| `params.php` | `params.php` (raíz) | Parámetros globales: IDs, tokens, URLs | ✅ Sí | 🔴 Contiene tokens hardcodeados |
| `main.php` | `backend/config/main.php` | Config principal backend | ✅ Sí | 🟢 Bajo |
| `main-local.php` | `backend/config/main-local.php` | Config local: DB, credenciales | ❌ No (gitignored) | 🔒 Correcto |
| `main-local.php` | `common/config/main-local.php` | Config local compartida | ❌ No (gitignored) | 🔒 Correcto |
| `rutas.php` | `backend/config/rutas.php` | Definición de todas las rutas | ✅ Sí | 🟢 Bajo |
| `cors.php` | `backend/config/cors.php` | Orígenes CORS permitidos | ✅ Sí | 🟡 Verificar en prod |
| `swagger.php` | `backend/config/swagger.php` | Config Swagger producción | ✅ Sí | 🟢 Bajo |
| `codeception.yml` | Raíz y `backend/` | Config de tests | ✅ Sí | 🟢 Bajo |
| `docker-compose.yml` | Raíz | Orquestación Docker | ✅ Sí | 🟢 Bajo |
| `Dockerfile` | Raíz | Imagen Docker | ✅ Sí | 🟢 Bajo |
| `.gitlab-ci.yml` | Raíz | Pipeline CI/CD | ✅ Sí | 🟢 Bajo |
| `daemons-app.json` | Raíz | Config daemons/workers | ✅ Sí | 🚧 Sin documentar |

---

## Assets de marca / imágenes embebidas

| Archivo | Ruta | Uso |
|---------|------|-----|
| `logoMuvin_rgb.png` | `backend/assets/` | Logo en PDFs y emails |
| `logo_bcr.png` | `backend/assets/` | Logo BCR en PDFs |
| `logo-profertil.png` | `backend/assets/` | Logo en PDFs fertilizantes |
| `fondo-blanco.jpg/png` | `backend/assets/` | Fondo para PDFs |
| `fondo.png` | `backend/assets/` | Fondo alternativo |
| `seguro.png` | `backend/assets/` | Icono seguro en PDFs |

---

## Plantillas de email

| Ruta | Propósito |
|------|-----------|
| `common/mail/` | Plantillas SwiftMailer (`.php` con HTML) |

> ⚠️ SwiftMailer está deprecado — ver [[deuda-tecnica]].

---

## Fixtures de tests

| Ruta | Propósito |
|------|-----------|
| `common/fixtures/` | Datos de prueba para Codeception/Faker |

---

## Hallazgos de auditoría

- 🔴 `params.php` en el repo contiene tokens de servicio hardcodeados (`s-roles-permisos.token: '123456'`). Este valor parece ser un placeholder de desarrollo, pero si se usa en producción es una vulnerabilidad crítica.
- 🔴 `console/log.cronAfip.txt` está commiteado. Expone logs de operación reales.
- ⚠️ Los scripts en `script/` son SQL manual sin trazabilidad. Deben migrarse al sistema de migraciones Yii2 o documentarse como procedimientos de mantenimiento.
- ⚠️ `daemons-app.json` no está documentado. Verificar si gestiona workers de la queue.
