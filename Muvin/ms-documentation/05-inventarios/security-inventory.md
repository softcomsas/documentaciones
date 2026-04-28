---
tags: [inventario, seguridad, riesgos]
última-revisión: 2026-04-27
---

# Inventario de seguridad

## Autenticación y autorización

| Aspecto | Estado | Severidad | Notas |
|---------|--------|:---------:|-------|
| Autenticación de mensajes TCP entrantes | 🚧 No implementada | 🟡 | Al no haber handlers, no aplica todavía. Deberá implementarse cuando se agreguen `@MessagePattern`. |
| Validación de firma/token en mensajes | 🚧 No implementada | 🟡 | Los contratos de `MsAuth` incluyen `validation['validate-key']` y `validation['validate-authorization']` — deberán usarse en los futuros handlers. |
| Autorización por scopes | 🚧 No implementada | 🟡 | Disponible vía `CMDS.auth.validate.authorization` cuando se implemente. |

## Gestión de secretos

| Aspecto | Estado | Severidad | Notas |
|---------|--------|:---------:|-------|
| Secretos en CI/CD | ✅ Correcto | 🟢 | Usa HashiCorp Vault. No hay secrets hardcodeados en workflows. |
| `DATABASE_URL` en código fuente | ✅ Correcto | 🟢 | Solo en `.env` (no versionado) y `.env-template` (sin valores reales). |
| Credenciales hardcodeadas | ✅ No detectadas | 🟢 | Sin credenciales en código fuente. |
| SSH keys en CI/CD | ✅ Correcto | 🟢 | Obtenidas desde Vault en runtime, no versionadas. |

## Imagen Docker

| Aspecto | Estado | Severidad | Notas |
|---------|--------|:---------:|-------|
| Usuario no-root en producción | ✅ Correcto | 🟢 | Dockerfile crea usuario `nestjs:nodejs` (uid 1001). No corre como root. |
| Multi-stage build | ✅ Correcto | 🟢 | Solo el artefacto compilado llega a la imagen final. Herramientas de dev no incluidas. |
| Versión fija de imagen base | ⚠️ `node:20-alpine` sin digest fijo | 🟡 | Considerar pin de digest para reproducibilidad total: `node:20-alpine@sha256:...` |

## Variables de entorno

| Aspecto | Estado | Severidad | Notas |
|---------|--------|:---------:|-------|
| Validación al arranque | ✅ Correcto | 🟢 | Joi valida presencia y tipo de todas las variables requeridas. App no arranca con config inválida. |
| `.env` en `.gitignore` | ✅ Correcto | 🟢 | No se versiona el archivo de entorno real. |

## Superficie de ataque

| Aspecto | Estado | Severidad | Notas |
|---------|--------|:---------:|-------|
| Endpoints HTTP expuestos | ✅ Ninguno | 🟢 | Solo TCP interno. Sin superficie HTTP pública. |
| Puerto TCP `4002` | ⚠️ Expuesto en red Docker `muvin-net` | 🟡 | Asegurar que el puerto no sea accesible desde fuera de la red Docker interna. |
| Inyección SQL | ✅ N/A actualmente | 🟢 | Sin consultas SQL. Cuando se implemente Prisma con modelos, usar siempre el ORM — nunca `$queryRaw` con input de usuario. |

## Resumen por severidad

| 🟢 Sin riesgo | 🟡 Atención | 🔴 Crítico |
|:---:|:---:|:---:|
| 8 | 4 | 0 |

## Hallazgos para abordar antes de producción

1. 🟡 Implementar autenticación en handlers `@MessagePattern` usando `MsAuth`
2. 🟡 Pinear digest de imagen Docker base
3. 🟡 Verificar que puerto `4002` no sea accesible desde fuera de `muvin-net`
4. 🟡 Definir política de autorización por scopes para cada handler que se implemente
