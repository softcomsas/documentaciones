# Modelo de Datos — Redis Muvin

> [!info]
> Este repositorio no tiene base de datos relacional ni modelo de entidades. Redis es en sí mismo el almacén de datos. Esta sección documenta las estructuras de datos que Redis maneja en el contexto de Muvin.

## Estructuras de datos en Redis

> [!note]
> Las claves y estructuras exactas dependen de los servicios consumidores (`api`, `sockets`). Este repo solo despliega Redis; no define el esquema de datos.

### Uso conocido por sockets (Node.js)

| Patrón de clave | Tipo Redis | Descripción probable |
|----------------|-----------|---------------------|
| `socket:*` | String / Hash | Estado de conexiones activas |
| `channel:*` | Pub/Sub channel | Canales de mensajería en tiempo real |

### Uso conocido por API (Yii2/PHP)

| Patrón de clave | Tipo Redis | Descripción probable |
|----------------|-----------|---------------------|
| `cache:*` | String | Caché de respuestas de API |
| `session:*` | Hash | Sesiones de usuario (si aplica) |

## Configuración del servidor Redis

Redis corre con configuración por defecto de `redis:7`:

| Parámetro | Valor por defecto | Implicancia |
|-----------|------------------|-------------|
| `maxmemory` | Sin límite | Redis puede consumir toda la RAM disponible |
| `maxmemory-policy` | `noeviction` | Si se llena la memoria, las escrituras fallan |
| `appendonly` | `no` | Sin persistencia AOF |
| `save` | Desactivado en contenedor | Sin snapshots RDB |
| `requirepass` | Sin contraseña | ⚠️ Ver [[deuda-tecnica]] |
| `bind` | `0.0.0.0` | Acepta conexiones de cualquier IP en la red |

> [!danger]
> Sin contraseña y sin restricción de bind, Redis es accesible para cualquier contenedor en la red `muvin-net` y potencialmente desde el host. Ver [[deuda-tecnica]].

## Referencias

- [[stack-tecnologico]]
- [[modulo-redis-container]]
- [[deuda-tecnica]]
