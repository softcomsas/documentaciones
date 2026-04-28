# Índice de Flujos Transversales

**Última actualización:** 2026-04-27

## Flujos Principales

### 1. Crear Contrato (Flujo Completo)

[[flujo-creacion-contrato|Ver flujo completo]]

```
Cliente → RPC create
        → Validar CUIT → Resolver IDs
        → Crear en BD
        → Notificar integrations
        → Registrar en logs
        → Retornar response
```

---

### 2. Validación y Autenticación

[[flujo-validacion-autenticacion|Ver flujo completo]]

```
Cliente → RPC validate-key
        → Buscar API key
        → Verificar firma HMAC
        → Retornar resultado
```

---

## Flujos Secundarios

### Buscar Contrato
1. Cliente envía reference + company_cuit
2. Servicio resuelve CUIT → ID
3. Busca en DB
4. Retorna datos

### Cambiar Balance
1. Cliente envía contract_id + new_balance
2. Servicio valida balance >= 0
3. Si balance = 0 → auto-cierre
4. Retorna status

---

## Documentos Relacionados

- [[flujo-creacion-contrato|Flujo de Creación]] - Detalle
- [[flujo-validacion-autenticacion|Flujo de Validación]] - Detalle
- [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades RPC]] - Cada acción

