# F-04 — Asignación Automática

## Descripción

Funcionalidad dentro de la pantalla de management que ejecuta un algoritmo automático para vincular cupos con solicitudes sin intervención manual del operador.

## Flujo

```
1. Operador hace clic en "Asignar Automáticamente"
2. POST → post-legacy-assign-auto con los cupos/solicitudes del contexto actual
3. Sistema procesa el matching y devuelve el resultado
4. AssignmentService refresca los datos (get-legacy-listado-zona)
5. Grilla semanal se actualiza con el nuevo estado
```

## Query HTTP

| Query | Método | Descripción |
|---|---|---|
| `post-legacy-assign-auto` | POST | Dispara la asignación automática en el backend legado |

## Notas

- Disponible solo desde `AssignmentManagementView`
- El algoritmo corre en el backend (`ms-legacy` / sistema legado)
- El frontend solo dispara y refresca — sin lógica de matching local
