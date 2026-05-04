# Módulo: Migrations

> **Ruta:** `source/migrations/`
> **Motor:** MySQL InnoDB

## Historial de migraciones

| # | Archivo | Fecha | Descripción |
|---|---------|-------|-------------|
| 1 | `m220615_210841_init.php` | 2022-06-15 | Crea tablas `user`, `role`, `permission`, `role_permission`, `user_role` |
| 2 | `m220630_143923_relations_m_n.php` | 2022-06-30 | Ajusta relaciones M:N |
| 3 | `m220630_195347_standar_columns.php` | 2022-06-30 | Agrega columnas estándar (`created_at`, `updated_at`, `deleted_at`) |
| 4 | `m221221_213926_alter_columns_.php` | 2022-12-21 | Altera columnas |
| 5 | `m222783_101221_alter_name_permission_column_permission_table.php` | — | Renombra columna `name` en tabla `permission` |

## Schema inicial (migración 1)

```sql
CREATE TABLE user (
  id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(60) NOT NULL,
  password_hash VARCHAR(60) NOT NULL,
  created_at INT NOT NULL,
  updated_at INT NOT NULL,
  deleted_at INT NULL,
  PRIMARY KEY (id),
  UNIQUE INDEX name_UNIQUE (name, deleted_at)
);

CREATE TABLE role (
  id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  created_at INT NOT NULL, updated_at INT NOT NULL, deleted_at INT NULL,
  PRIMARY KEY (id),
  UNIQUE INDEX name_UNIQUE (name, deleted_at)
);

CREATE TABLE permission (
  id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  service VARCHAR(100) NOT NULL,
  type VARCHAR(7) NOT NULL,
  created_at INT NOT NULL, updated_at INT NOT NULL, deleted_at INT NULL,
  PRIMARY KEY (id),
  UNIQUE INDEX name_UNIQUE (name, deleted_at),
  UNIQUE INDEX service_INDEX (service, type, deleted_at)
);

CREATE TABLE role_permission (
  id_role INT NOT NULL,
  id_permission INT NOT NULL,
  created_at INT NOT NULL,
  PRIMARY KEY (id_role, id_permission),
  FK role_permission.id_role → role.id,
  FK role_permission.id_permission → permission.id
);

CREATE TABLE user_role (
  id_user INT NOT NULL,
  id_role INT NOT NULL,
  created_at INT NOT NULL,
  PRIMARY KEY (id_user, id_role),
  FK user_role.id_user → user.id,
  FK user_role.id_role → role.id
);
```

## Notas

- Los índices `UNIQUE` en `(name, deleted_at)` permiten reutilizar nombres tras un soft delete (el registro eliminado tiene `deleted_at != NULL`, el nuevo tiene `deleted_at = NULL` → no hay conflicto).
- Las migraciones 2-5 no están documentadas en el código con comentarios claros — revisar cada archivo para detalle.

## Ejecución

```bash
# Dentro del container o con PHP disponible
php yii migrate
php yii migrate/up
php yii migrate/down 1  # Revertir última migración
```
