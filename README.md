<p align="center">
  <img src="https://img.shields.io/badge/%F0%9F%93%9A-Documentaciones-blue?style=for-the-badge" alt="Documentaciones" />
</p>

<h1 align="center">Documentaciones</h1>

<p align="center">
  Repositorio centralizado de documentación técnica para todos mis proyectos.<br/>
  Arquitectura · APIs · Modelos de datos · Flujos · Decisiones técnicas
</p>

<p align="center">
  <img src="https://img.shields.io/badge/estado-activo-brightgreen?style=flat-square" alt="Estado" />
  <img src="https://img.shields.io/badge/formato-Markdown-orange?style=flat-square" alt="Formato" />
  <img src="https://img.shields.io/badge/diagramas-Mermaid-ff69b4?style=flat-square" alt="Diagramas" />
</p>

---

## 📖 ¿Qué es esto?

Un mono-repo de **documentación técnica** donde centralizo el conocimiento de cada proyecto en el que trabajo. Cada carpeta raíz representa un proyecto independiente con su propia estructura interna.

> La idea es tener un único lugar de referencia que sea fácil de buscar, mantener y compartir.

---

## 🗂️ Proyectos documentados

| Proyecto | Descripción | Stack principal |
|----------|-------------|-----------------|
| [**Muvin**](./Muvin/) | Panel de administración multi-rol para logística de transporte de granos y fertilizantes (Argentina) | Angular 6 · TypeScript · Firebase · Docker |

> *Se irán agregando más proyectos a medida que se documenten.*

---

## 🏗️ Estructura tipo por proyecto

Cada proyecto sigue (cuando aplica) una estructura similar:

```
NombreProyecto/
├── README.md                    # Visión general del proyecto
├── 00-overview/                 # Stack, contexto, decisiones clave
├── 01-arquitectura/             # Diagramas, dependencias, módulos
├── 01-modulos/                  # Detalle de cada módulo funcional
├── 03-servicios-backend/        # Endpoints, contratos de API
├── 04-modelo-de-datos/          # Entidades, diagrama E-R
├── 05-inventarios/              # Archivos, reportes, seguridad
├── 06-flujos-transversales/     # Flujos de negocio end-to-end
└── 08-riesgos-y-deuda-tecnica/  # Deuda técnica, hotspots, roadmap
```

> La numeración permite ordenar las secciones de forma lógica y dejar huecos para futuras categorías.

---

## 🚀 Cómo navegar

1. **Entrá a la carpeta del proyecto** que te interese.
2. Cada proyecto tiene su propio `README.md` con un overview y diagrama de arquitectura.
3. Desde ahí podés profundizar en la sección que necesites.
4. Los diagramas usan **Mermaid** — se renderizan nativamente en GitHub.

---

## ✍️ Convenciones

| Aspecto | Convención |
|---------|------------|
| Formato | Markdown (`.md`) |
| Diagramas | Mermaid (inline en Markdown) |
| Idioma | Español (código y nombres técnicos en inglés cuando corresponda) |
| Estructura | Carpetas numeradas por categoría |
| Índices | Archivos `_indice-*.md` dentro de cada sección |

---

## 🧜 Cómo ver los diagramas Mermaid en escritorio

Los archivos de este repo incluyen diagramas **Mermaid** embebidos en Markdown. GitHub los renderiza automáticamente, pero si querés verlos **de forma local** tenés varias opciones:

---

### Opción 1 — VS Code + Extensión (recomendada)

La forma más práctica si ya usás VS Code para editar los `.md`.

| Paso | Acción |
|------|--------|
| **1** | Abrí VS Code — [Descargar VS Code](https://code.visualstudio.com/Download) |
| **2** | Instalá la extensión **Markdown Preview Mermaid Support**: <br/> → Ir a Extensiones (`Ctrl+Shift+X`) <br/> → Buscar `bierner.markdown-mermaid` <br/> → Click en **Instalar** <br/> → [Link directo al Marketplace](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-mermaid) |
| **3** | Abrí cualquier archivo `.md` del repo |
| **4** | Presioná `Ctrl+Shift+V` para abrir la **vista previa de Markdown** |
| **5** | Los bloques ` ```mermaid ` se renderizan como diagramas interactivos ✅ |

> **Tip:** Usá `Ctrl+K V` para abrir la preview lado a lado mientras editás.

---

### Opción 2 — Obsidian (visor de documentación completo)

Ideal si querés navegar toda la documentación como una wiki local con links entre archivos.

| Paso | Acción |
|------|--------|
| **1** | Descargá **Obsidian** — [https://obsidian.md/download](https://obsidian.md/download) |
| **2** | Abrí Obsidian → **"Open folder as vault"** |
| **3** | Seleccioná la carpeta raíz de este repo (`documentaciones/`) |
| **4** | Navegá los archivos `.md` desde el panel lateral |
| **5** | Los diagramas Mermaid se renderizan automáticamente (soporte nativo) ✅ |

> Obsidian es gratuito para uso personal y no requiere cuenta.

---

### Opción 3 — Mermaid CLI (exportar diagramas a imagen)

Para generar archivos `.svg` o `.png` desde la terminal.

| Paso | Acción |
|------|--------|
| **1** | Instalá Node.js si no lo tenés — [https://nodejs.org](https://nodejs.org) |
| **2** | Instalá Mermaid CLI de forma global: |

```bash
npm install -g @mermaid-js/mermaid-cli
```

| Paso | Acción |
|------|--------|
| **3** | Creá un archivo `.mmd` con el contenido del diagrama (sin los backticks): |

```bash
# Ejemplo: extraer un diagrama de un .md y guardarlo como .mmd
# Luego renderizar:
mmdc -i diagrama.mmd -o diagrama.svg
mmdc -i diagrama.mmd -o diagrama.png
```

| Paso | Acción |
|------|--------|
| **4** | El archivo de salida (`.svg` / `.png`) se genera en la misma carpeta ✅ |

> **Docs oficiales:** [https://github.com/mermaid-js/mermaid-cli](https://github.com/mermaid-js/mermaid-cli)

---

### Opción 4 — Mermaid Live Editor (online, sin instalar nada)

Si solo necesitás ver o editar un diagrama rápido sin instalar nada.

| Paso | Acción |
|------|--------|
| **1** | Abrí [https://mermaid.live](https://mermaid.live) en el navegador |
| **2** | Copiá el contenido de un bloque `mermaid` de cualquier `.md` |
| **3** | Pegalo en el editor de la izquierda |
| **4** | El diagrama se renderiza en tiempo real a la derecha ✅ |
| **5** | Podés exportar como `.svg`, `.png` o compartir el link |

---

### Resumen rápido

| Herramienta | Tipo | Mermaid | Ideal para |
|-------------|------|---------|------------|
| **VS Code + extensión** | Editor | ✅ Extensión | Editar y previsualizar día a día |
| **Obsidian** | Visor/Wiki | ✅ Nativo | Navegar toda la documentación |
| **Mermaid CLI** | Terminal | ✅ Export | Generar imágenes para presentaciones |
| **Mermaid Live** | Web | ✅ Online | Edición rápida sin instalar nada |

---

## 📌 Cómo agregar un nuevo proyecto

```bash
# 1. Crear la carpeta raíz
mkdir NuevoProyecto

# 2. Crear el README principal
touch NuevoProyecto/README.md

# 3. Agregar las secciones que apliquen
mkdir NuevoProyecto/00-overview
mkdir NuevoProyecto/01-arquitectura
# ... etc.

# 4. Actualizar la tabla de proyectos en este README
```

---

<p align="center">
  <sub>Hecho con 📝 y mucho café por <a href="https://rodolfovazquez.com">Rodolfo Vázquez</a></sub>
</p>
