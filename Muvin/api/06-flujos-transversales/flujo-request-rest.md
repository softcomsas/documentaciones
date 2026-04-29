# Flujo: Request REST completo

> **Aplica a:** módulos `logs` y `temporary`
> **Última revisión:** 2026-04-29

---

## Ciclo de vida de un request REST

```mermaid
sequenceDiagram
    autonumber
    participant C as Cliente
    participant EX as Express HTTP
    participant VP as ValidationPipe
    participant AEF as AllExceptionsFilter
    participant CTRL as Controller
    participant SVC as Service
    participant EXT as Microservicio / HTTP externo

    C->>EX: HTTP Request + body/query
    EX->>VP: Instanciar y validar DTO

    alt DTO inválido
        VP-->>C: 400 Bad Request\n{message: [...errores], statusCode: 400}
    else DTO válido
        VP->>CTRL: DTO transformado
        CTRL->>SVC: método del servicio(DTO)

        alt Error en servicio (logs)
            SVC->>SVC: catch → LOG(error)
            SVC-->>CTRL: undefined
            CTRL-->>C: 200 undefined (silencioso)
        else Error en servicio (temporary)
            SVC->>SVC: _throwExternalError(err)
            SVC-->>AEF: HttpException
            AEF-->>C: {statusCode, message}
        else Éxito
            SVC->>EXT: ClientProxy.emit/send o Axios
            EXT-->>SVC: respuesta
            SVC-->>CTRL: datos
            CTRL-->>C: 200 + datos
        end
    end
```

---

## Comportamiento por módulo

| Módulo | Error en servicio | Respuesta al cliente |
|--------|------------------|----------------------|
| `logs` | Silenciado con `catch` + `LOG()` | `200 undefined` |
| `temporary` | `HttpException` propagada | Status del servicio externo o `502` |

---

## Configuración de ValidationPipe

```typescript
new ValidationPipe({
  transform: true,           // Transforma tipos (string → number, etc.)
  whitelist: true,           // Elimina propiedades no declaradas en el DTO
  forbidNonWhitelisted: true // Lanza error si hay propiedades extra
})
```

---

## Referencias

- [[arquitectura-general]]
- [[modulo-logs]]
- [[modulo-temporary]]
