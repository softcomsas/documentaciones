# Visión General — Muvinapp API Panel

> **Última revisión:** 2026-04-21
> **Ver también:** [[stack-tecnologico]], [[arquitectura-alto-nivel]], [[glosario]]

---

## ¿Qué es Muvinapp?

**Muvinapp** es una plataforma de gestión logística agropecuaria argentina. Su función central es **coordinar el transporte de granos** desde los establecimientos productores hasta los puertos y terminales de descarga, integrando múltiples actores de la cadena de valor:

| Actor | Rol en el sistema |
|-------|------------------|
| **Centros / Terminales** | Publican cupos de descarga disponibles |
| **Choferes** | Reciben y aceptan cupos de viaje |
| **Transportistas** | Administran su flota de camiones y choferes |
| **Corredores** | Intermedian entre centros y transportistas |
| **Cargadores / Remitentes** | Dueños de la carga |
| **Destinatarios** | Receptores de la carga en destino |
| **Entregadores** | Operadores de entrega en destino |
| **Operadores** | Personal del centro/terminal |

---

## Dominio de negocio

El flujo de negocio principal es:

```
Centro publica cupo → Corredor/Transportista demanda → Chofer toma → Viaje → Descarga
```

Un **cupo** es la unidad atómica del sistema: representa el permiso de una terminal para recibir un camión en una fecha y hora determinadas, para descargar una cantidad de grano de un producto y cosecha específicos.

La **Carta de Porte Electrónica (CPe)** es el documento obligatorio de AFIP que acompaña cada viaje de granos, vinculando la carga al cupo.

---

## Contexto regulatorio

El sistema opera en el marco de la **Resolución AFIP 5017/2021** que regula la Carta de Porte Electrónica, y bajo las normas del **MAGYP** (Ministerio de Agricultura, Ganadería y Pesca) para el control de granos y agroquímicos.

---

## Alcance del repositorio `api-panel`

Este repositorio contiene **exclusivamente el backend** (API REST):

| Componente | Descripción |
|-----------|-------------|
| `backend/` | Controllers + Behaviours + Assets |
| `common/` | Models (ActiveRecord) + Components + Jobs |
| `console/` | Comandos CLI + Migraciones |
| `vendor/` | Dependencias Composer |

El frontend (apps web y móvil) consume esta API por HTTP.

---

## Versiones del API

| Prefijo | Estado | Descripción |
|---------|--------|-------------|
| `v1` | Activo | Base principal (sin prefijo de módulo) |
| `v2` | Legacy | Cupos pre-CCPP |
| `v3` | Activo | Cupos con Carta de Porte Electrónica |
| `magyp` | Activo | Integración MAGYP/AFIP |
| `mtr` | Activo | Integración MTR (MATba) |
| `fertilizantes` | Activo | Cupos de fertilizantes |
| `agroquimicos` | Activo | Pedidos de agroquímicos |
| `turneada` | Activo | Sistema de turnos |
| `bot` | Activo | ChatBot WhatsApp |
| `erp` | Activo | Integración ERP externo |

---

## Principios de diseño

- **REST** sobre HTTP/HTTPS
- **JWT** como mecanismo de autenticación
- **RBAC** con 16 roles de negocio
- **Yii2** como framework PHP
- **ActiveRecord** para acceso a MySQL
- **Queue** (yii2-queue) para operaciones asíncronas
- **Swagger/OpenAPI** para documentación de endpoints

---

## Integraciones externas clave

| Sistema | Propósito |
|---------|-----------|
| AFIP/STOP | Validación CTG y Carta de Porte |
| MAGYP | Cadena agroalimentaria |
| MATba/Rofex (MTR) | Carátulas de mercado a término |
| Bus Integración (VTerra) | Sincronización con ERP |
| Infobip SMS | Notificaciones SMS a choferes |
| WhatsApp Business | ChatBot de operación |
| MS RBAC | Microsservicio de roles/permisos |
| MS Logística | Microsservicio logístico |
| MS Documentaciones | Generación de PDFs |
| Gateway API | Log de peticiones externas |

---

## Números del sistema (aproximados)

| Métrica | Valor |
|---------|-------|
| Controladores | ~170 |
| Migraciones DB | 623+ |
| Modelos ActiveRecord | 84 |
| Módulos | 10 |
| Roles de negocio | 16 |
| Años de desarrollo activo | 8+ |
