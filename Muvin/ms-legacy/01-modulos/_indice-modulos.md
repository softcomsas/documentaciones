# Índice de módulos

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Módulos del sistema

| # | Módulo | Descripción breve | Criticidad | Enlace |
|---|--------|-------------------|-----------|--------|
| 1 | Controlador / Entrada TCP | Recibe mensajes del ecosistema Muvin por TCP | 🔴 Alta | [[modulo-controller]] |
| 2 | Servicio / Proxy HTTP | Ejecuta las llamadas al backend legacy | 🔴 Alta | [[modulo-service]] |
| 3 | API / Query Registry | Registra y define las queries disponibles | 🟡 Media | [[modulo-api]] |
| 4 | Config / Entorno | Carga y valida variables de entorno | 🔴 Alta | [[modulo-config]] |
| 5 | Common / Utilitarios | Logger, función identidad, tipos genéricos | 🟢 Baja | [[modulo-common]] |
| 6 | Contratos | Define la API pública del microservicio | 🟡 Media | [[modulo-contracts]] |
| 7 | Types / Sistema de tipos | Tipos internos del proxy genérico | 🟢 Baja | [[modulo-types]] |

## Nota sobre la estructura modular

> [!info] Microservicio pequeño y enfocado
> `muvin-ms-legacy` tiene un único flujo de negocio: recibir una solicitud TCP → proxy HTTP → retornar respuesta normalizada. No existen módulos de dominio separados (no hay usuarios, no hay facturación, no hay autenticación propia). La complejidad reside en el **sistema de tipado genérico** y en la **extensibilidad** del patrón de queries.
