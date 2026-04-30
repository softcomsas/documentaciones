# api-wscpe — Documentación Técnica

> **Proyecto:** `api-wscpe`
> **Stack:** PHP 7.4 · Yii2 Basic · REST API · SOAP (AFIP WSCPE)
> **Propósito:** Microservicio de consulta de CPE (Carta de Porte Electrónica) al Webservice de AFIP (`wscpe`). Actúa como proxy autenticado entre los sistemas Muvin y el servicio SOAP de AFIP.

---

## Índice de secciones

| Sección | Descripción | Enlace |
|---------|-------------|--------|
| 00 · Visión general | Propósito, contexto, arquitectura | [00-overview/vision-general.md](./00-overview/vision-general.md) |
| 00 · Arquitectura alto nivel | Diagrama de componentes y flujos | [00-overview/arquitectura-alto-nivel.md](./00-overview/arquitectura-alto-nivel.md) |
| 00 · Stack tecnológico | PHP, Yii2, dependencias | [00-overview/stack-tecnologico.md](./00-overview/stack-tecnologico.md) |
| 00 · Glosario | CPE, CTG, AFIP, WSCPE, TA | [00-overview/glosario.md](./00-overview/glosario.md) |
| 01 · Módulos | Controllers y componentes | [01-modulos/_indice-modulos.md](./01-modulos/_indice-modulos.md) |
| 02 · Funcionalidades | Endpoints y casos de uso | [02-funcionalidades/_indice-funcionalidades.md](./02-funcionalidades/_indice-funcionalidades.md) |
| 03 · Servicios backend | Endpoints REST expuestos | [03-servicios-backend/_indice-servicios.md](./03-servicios-backend/_indice-servicios.md) |
| 04 · Modelo de datos | Tablas y entidades | [04-modelo-de-datos/_indice-entidades.md](./04-modelo-de-datos/_indice-entidades.md) |
| 05 · Inventarios | Estructura, dependencias, seguridad | [05-inventarios/tree-estructura-archivos.md](./05-inventarios/tree-estructura-archivos.md) |
| 06 · Flujos transversales | Auth + consulta CPE end-to-end | [06-flujos-transversales/_indice-flujos.md](./06-flujos-transversales/_indice-flujos.md) |
| 07 · Operación y despliegue | Docker, env, configuración | [07-operacion-y-despliegue/requisitos-entorno.md](./07-operacion-y-despliegue/requisitos-entorno.md) |
| 08 · Riesgos y deuda técnica | Hotspots, deuda, recomendaciones | [08-riesgos-y-deuda-tecnica/hotspots.md](./08-riesgos-y-deuda-tecnica/hotspots.md) |

---

## Contexto en el ecosistema Muvin

```mermaid
graph LR
    MuvinBackend["api / api-bus\n(NestJS / PHP)"]
    ApiWscpe["api-wscpe\n(PHP Yii2)"]
    AFIP["AFIP\nwscpe SOAP\n(prod/QA)"]

    MuvinBackend -->|POST /cpe {cuit, ctg}| ApiWscpe
    ApiWscpe -->|SOAP ConsultarCPEAutomotor| AFIP
    AFIP -->|datos CPE| ApiWscpe
    ApiWscpe -->|JSON response + cache| MuvinBackend
```

`api-wscpe` es un microservicio especializado: su única función operacional es consultar el estado de una CPE (Carta de Porte Electrónica) en AFIP dado un CUIT y un número de CTG. Cachea los resultados en base de datos para evitar llamadas repetidas al webservice externo.
