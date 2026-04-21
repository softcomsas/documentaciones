# Inventario de Seguridad

> **Contexto:** [[deuda-tecnica]] · [[arquitectura-alto-nivel]]

## Resumen ejecutivo

`ms-logs` es un microservicio **interno** expuesto solo en red privada via TCP sin autenticación. El modelo de seguridad asume que ningún cliente malicioso puede alcanzar el puerto TCP directamente. Si esa suposición falla, el servicio carece de controles.

## Superficies de ataque

| Superficie | Estado | Severidad |
|------------|--------|-----------|
| TCP sin TLS | Sin cifrado en tránsito | 🟡 Medio (red privada) |
| Sin autenticación de mensajes | Cualquier proceso en red puede enviar mensajes | 🟡 Medio |
| Credenciales Docker en docker-compose.yml | `muvin/muvin`, `root/root` hardcodeadas | 🔴 Alto |
| Errores silenciados | No hay alertas ante fallos — oculta incidentes | 🟡 Medio |
| `DATABASE_URL` en env var | Sin rotación ni gestión de secretos | 🟡 Medio |

## Detalle por ítem

### TCP sin TLS

NestJS microservices TCP **no soporta TLS nativamente**. Todo el tráfico entre el gateway y `ms-logs` viaja en texto plano. En una red interna de contenedores esto es aceptable, pero ante una exfiltración de la red interna todos los payloads de usuario son visibles.

**Mitigación posible:** Usar un service mesh (Istio, Linkerd) para mTLS transparente, o migrar a NATS/Kafka con TLS configurado.

### Sin autenticación de mensajes

No existe validación de que el emisor de un mensaje TCP sea quien dice ser. Cualquier proceso con acceso al puerto `LOGS_MICROSERVICE_PORT` puede enviar mensajes arbitrarios.

**Impacto:** Contaminación de datos de auditoría, inyección de logs falsos.

### Credenciales hardcodeadas en docker-compose.yml

```yaml
# docker/docker-compose.yml
MYSQL_ROOT_PASSWORD: root
MYSQL_PASSWORD: muvin
```

Estas credenciales están en el repositorio git. Si el repo es accesible externamente, la BD de desarrollo está comprometida.

**Fix inmediato:** Usar variables de entorno con `.env` en gitignore para docker-compose.

### Errores silenciados con `console.log`

Todos los bloques catch en el servicio usan `console.log(error)` sin propagar ni alertar. Ante una falla de BD, el servicio da apariencia de funcionamiento normal mientras descarta logs silenciosamente.

**Impacto:** Pérdida de datos de auditoría sin detección.

### Ausencia de `.env.example`

No existe un archivo `.env.example` ni documentación de las variables requeridas dentro del repositorio. Un developer nuevo puede iniciar el servicio sin configurar `DATABASE_URL` y recibir solo un error de arranque críptico.

---

*Ver también: [[deuda-tecnica]] · [[build-y-despliegue]] · [[requisitos-entorno]]*
