# F-16 · Menú Lateral por Perfil

> **Módulo:** [modulo-home](../01-modulos/modulo-home.md)

## Descripción

El menú lateral (`Drawer`) se construye dinámicamente en función de los flags de perfil almacenados en `SharedPreferences`. No hay recarga de perfil desde el backend; se usa lo que se guardó en el último login.

## Ítems del menú

| Ítem | Ruta | Condición de visibilidad |
|------|------|--------------------------|
| 🏠 Home | `/home` | Siempre |
| 📦 Gestión Cupos | `/cupos` | `esDadorCupo == true` |
| 📦 Gestión Cupos CF | `/cuposcf` | `esClienteFinal == true` |
| 🚛 Gestión Cargas | `/cargas` | Siempre (si autenticado) |
| 🚪 Salir | — | Siempre |

## Riesgos

- ⚠️ Si el backend cambia el perfil del usuario (ej. revoca el rol de dador), la app no lo reflejará hasta el próximo login.
