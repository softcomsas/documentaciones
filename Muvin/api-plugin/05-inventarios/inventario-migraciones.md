# Inventario de Migraciones

**Ruta:** `source/migrations/`  
**Total:** ~75 migraciones (octubre 2021 — marzo 2025)

## Cronología de migraciones

### 2021 — Inicio del proyecto

| Migración | Cambio |
|---|---|
| `m211004_142137_create_mail_table` | Tabla inicial de mails |
| `m211006_190550_create_cupo_doc_notificaciones` | Notificaciones de cupo |
| `m211007_140515_create_cupo_mail_notif` | Tabla cupo_mail_notif |
| `m211012_143912_insert_errores_mail` | Tipos de error de mail |
| `m211018_175302_modify_cupo_documento_nombre_archivo` | Ajuste campo nombre_archivo |

### 2022 — Expansión de empresas y refactorización

| Migración | Cambio |
|---|---|
| `m220407_200635_create_oncca_destino_producto_table` | Tabla ONCCA |
| `m220411_131318_modify_cupo_documento_fields` | Ajuste campos cupo_documento |
| `m220419_125925_add_email_template_fechavenc` | Template email con fecha vencimiento |
| `m220701_223239_drop_proceso_table` | ⚠️ Drop completo de tabla proceso |
| `m220701_223309_create_proceso_table` | Recreación tabla proceso (nuevo esquema) |
| `m220701_223454_proceso_add` | Inserción de procesos iniciales |
| `m220705_132049_cuit_empresa_create_table` | Tabla cuit_empresas |
| `m220708_151447_destinos_dreyfus_create_table` | Tabla destinos_dreyfus |
| `m220718_alter_table_cupo_mail_notif` | Ajustes notificaciones |
| `m220718_alter_table_email_template` | Ajustes templates |
| `m220718_193952_alter_cliente_add_credenciales` | ⭐ Agrega user_muvin/password a cliente |
| `m220725_143558_alter_table_cupo_documento` | Ajuste cupo_documento |
| `m220727_alter_table_cliente_datos` | Ajuste datos de cliente |
| `m220727_alter_table_cliente_configuracion` | Ajuste cliente_configuracion |
| `m220805_153116_alter_table_cupo_documento` | Ajuste cupo_documento |
| `m220808_190311_proceso_add` | Nuevos procesos |
| `m220811_*` | Nuevo cliente + proceso + configuración |

### 2023 — Alta de nuevos clientes (en masa)

Las migraciones de 2023 siguen un patrón repetitivo de alta de clientes:
- `*_cliente_add` → nuevo cliente
- `*_insert_proceso_add` → proceso para el cliente
- `*_cliente_configuracion_add` → configuración IMAP
- `*_insert_template_mail` → template de email
- `*_insert_cupo_mail_notif_add` → configuración de notificaciones

Grupos de alta (por fecha):
- `2023-04-11`: 3 clientes/configuraciones
- `2023-07-31`: 2 clientes
- `2023-08-10`: 2 clientes
- `2023-08-16`: 2 clientes
- `2023-08-30`: 4 clientes (incluyendo `m230830_150502_cliente_add`)
- `2023-09-04`: 2 clientes
- `2023-09-11`: Actualización configuraciones + eliminación templates/procesos
- `2023-10`: Actualización templates y procesos

### 2025 — PluginV2 (DescargasRT)

| Migración | Cambio |
|---|---|
| `m250131_091817_create_table_rt` | ⭐ Tabla descargas_rt (PluginV2) |
| `m250203_135904_insert_rt_config_monsanto` | Configuración RT Monsanto |
| `m250219_192453_add_new_column_to_descargas_rt` | Columnas adicionales descargas_rt |
| `m250305_172935_insert_rt_config_monsanto` | Config RT adicional |
| `m250313_142747_create_descargas_rt_notif_table` | Tabla descargas_rt_notif |
| `m250317_160700_add_email_template` | Template para notificaciones RT |
| `m250325_181646_update_proceso` | Actualización proceso RT |

## Observaciones

- El alto número de migraciones `*_cliente_add` sugiere que la incorporación de nuevos clientes se hace a nivel de migración de BD (no hay un panel de administración visible)
- La recreación completa de la tabla `proceso` en julio 2022 indica un cambio de arquitectura significativo
- Las migraciones de PluginV2 en 2025 muestran que el producto está activo y en evolución
