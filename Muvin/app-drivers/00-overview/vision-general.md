# Visión General — app-drivers

> [[arquitectura-alto-nivel]] | [[stack-tecnologico]] | [[glosario]]

## ¿Qué es app-drivers?

`app-drivers` (package `muvin_latam_app`) es la **aplicación móvil para choferes** del ecosistema Muvin. Su propósito es digitalizar la operación logística del chofer: ver qué cargas están disponibles, postularse a ellas, consultar el viaje asignado y subir la documentación del transporte (remito / carta de porte).

## A quién sirve

- **Choferes de camión** registrados en la plataforma Muvin Latam.
- Opera sobre Android e iOS (Flutter cross-platform).

## Flujo de negocio resumido

```
Chofer instala la app
  → Registra su teléfono (código SMS)
  → Ve cargas públicas disponibles
  → Se postula a una carga
  → Operador Muvin asigna el viaje
  → Chofer ve su viaje asignado en la app
  → Chofer sube foto del remito inicial → viaje en curso
  → Chofer sube foto del remito final → descarga confirmada
  → App reporta GPS cada 10s mientras el chofer está en Home
```

## Estado actual

| Aspecto | Estado |
|---------|--------|
| Plataformas | Android ✅, iOS ✅ (build) |
| API base URL | `https://pruebas.muvinapp.com/api/backend/web/` ⚠️ URL de *pruebas* hardcodeada |
| Dart SDK | >=2.1.0 <3.0.0 (sin null safety — obsoleto) |
| CartaPortePage | 🔴 **Rota** — lanza NullPointerException al renderizar |
| SignupPage | 💀 Vacía — sólo devuelve `Container()` |
| Tests | Solo test por defecto de Flutter (sin tests reales) |

## Números del proyecto

| Métrica | Valor |
|---------|-------|
| Archivos Dart | 23 |
| Pages | 11 (9 implementadas, 2 vacías/rotas) |
| Endpoints REST consumidos | 10 |
| Dependencias producción | 14 |
| Assets (imágenes) | 7 |
