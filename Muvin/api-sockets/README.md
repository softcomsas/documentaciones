# api-sockets — Documentación Técnica

> **Proyecto:** `api-sockets`
> **Stack:** Node.js · TypeScript · Express 4 · Socket.IO 2 · Redis
> **Propósito:** Servidor WebSocket en tiempo real del ecosistema Muvin. Gestiona presencia de usuarios conectados, mensajería broadcast/privada, notificaciones push a usuarios por `id_persona`, y despacho de consultas a choferes.

---

## Índice de secciones

| Sección | Descripción | Enlace |
|---------|-------------|--------|
| 00 · Overview | Visión general, arquitectura, stack, glosario | [00-overview/vision-general.md](./00-overview/vision-general.md) |
| 01 · Módulos | Clases y componentes internos | [01-modulos/_indice-modulos.md](./01-modulos/_indice-modulos.md) |
| 02 · Funcionalidades | Casos de uso y eventos | [02-funcionalidades/_indice-funcionalidades.md](./02-funcionalidades/_indice-funcionalidades.md) |
| 03 · Servicios backend | REST endpoints + Socket.IO events | [03-servicios-backend/_indice-servicios.md](./03-servicios-backend/_indice-servicios.md) |
| 04 · Modelo de datos | Entidades en memoria y Redis | [04-modelo-de-datos/_indice-entidades.md](./04-modelo-de-datos/_indice-entidades.md) |
| 05 · Inventarios | Árbol de archivos, seguridad, dependencias | [05-inventarios/tree-estructura-archivos.md](./05-inventarios/tree-estructura-archivos.md) |
| 06 · Flujos transversales | Diagramas de secuencia end-to-end | [06-flujos-transversales/_indice-flujos.md](./06-flujos-transversales/_indice-flujos.md) |
| 07 · Operación y despliegue | Requisitos, configuración, Docker | [07-operacion-y-despliegue/_indice-operacion.md](./07-operacion-y-despliegue/_indice-operacion.md) |
| 08 · Riesgos y deuda técnica | Hotspots, deuda, recomendaciones | [08-riesgos-y-deuda-tecnica/_indice-riesgos.md](./08-riesgos-y-deuda-tecnica/_indice-riesgos.md) |

---

## Contexto en el ecosistema Muvin

```
app / app-agronomy / app-panel
        │  WebSocket (Socket.IO v2)
        ▼
  api-sockets (:5000)
   ├── Redis (presencia persistente)
   └── Express REST (llamado desde api-panel / api)
```

- **Clientes WebSocket:** `app` (Angular), `app-agronomy`, `app-panel`.
- **Emisores REST:** `api-panel` llama `POST /notificacion`, `POST /consulta`, `POST /chofer-respuesta-consulta/:id`, etc.
- **Redis:** Almacena la lista de usuarios conectados para sobrevivir reinicios del proceso Node.

---

## Inicio rápido

```bash
# Instalar dependencias
npm install

# Compilar TypeScript
npx tsc

# Arrancar (requiere Redis corriendo)
PORT=5000 REDIS_URL=redis://localhost:6379 node dist/
```
