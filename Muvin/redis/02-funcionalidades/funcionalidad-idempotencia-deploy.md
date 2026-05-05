# Funcionalidad: Idempotencia del Deploy

## Descripción

El script de deploy puede ejecutarse múltiples veces sin efectos secundarios. Siempre deja el sistema en el estado correcto independientemente del estado previo.

## Mecanismos de idempotencia

### 1. Creación de red condicionada

```bash
if ! docker network inspect $NETWORK_NAME > /dev/null 2>&1; then
  docker network create $NETWORK_NAME
fi
```
- Si la red ya existe → no hace nada.
- Si no existe → la crea.

### 2. Eliminación del contenedor previo

```bash
docker rm -f $CONTAINER_NAME || true
```
- Si el contenedor existe → lo elimina forzosamente.
- Si no existe → el `|| true` evita que el script falle.
- Garantiza que siempre se crea un contenedor limpio con la imagen más reciente.

## Implicación operativa

> [!important]
> Al hacer `docker rm -f` + `docker run`, hay una **ventana de downtime** entre la eliminación y la creación del nuevo contenedor. Si Redis está siendo consumido activamente, los clientes perderán conexión momentáneamente.
>
> En producción esto puede requerir estrategia de rolling update o uso de Sentinel/Cluster Redis.

## Referencias

- [[modulo-deploy-dev]]
- [[funcionalidad-deploy-automatico]]
- [[deuda-tecnica]]
