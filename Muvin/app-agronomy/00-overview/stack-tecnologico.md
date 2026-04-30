# Stack Tecnológico — App Agronomy

> **Última revisión:** 2026-04-30

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|------------|---------|-----------|---------------|--------|
| Angular | 16.2.x | Framework SPA principal | Vigente (LTS activo) | 🟢 |
| Angular Material | 16.2.x | Componentes UI Material Design | Vigente | 🟢 |
| Angular CDK | 16.2.x | Kit de componentes base | Vigente | 🟢 |
| Angular Router | 16.2.x | Enrutamiento SPA (lazy loading) | Vigente | 🟢 |
| Angular Forms | 16.2.x | Formularios reactivos y template-driven | Vigente | 🟢 |
| Angular Service Worker | 16.2.x | PWA / caché offline | Vigente | 🟢 |
| Angular Google Maps | 16.2.x | Integración Google Maps | Vigente | 🟢 |
| Angular Flex Layout | 15.0.0-beta.42 | Layout utilitario CSS | ⚠️ Deprecado por Angular team | 🔴 |
| @ngx-translate/core | 14.0.0 | Internacionalización (i18n) | Vigente | 🟢 |
| Bootstrap | 5.2.3 | CSS utility / grid | Vigente | 🟢 |
| RxJS | ~7.5.0 | Programación reactiva | Vigente | 🟢 |
| Moment.js | ^2.29.4 | Manejo de fechas | Mantenimiento (sin nuevas features) | 🟡 |
| ApexCharts / ng-apexcharts | ^3.35.5 / ^1.7.1 | Gráficos interactivos | Vigente | 🟢 |
| ECharts / ngx-echarts | ^5.3.3 / ^14.0.0 | Gráficos avanzados | Vigente | 🟢 |
| Chart.js / ng2-charts | ^3.9.1 / ^4.0.0 | Gráficos (uso secundario) | Vigente | 🟢 |
| Quill / ngx-quill | ^1.3.7 / ^23.0.0 | Editor de texto enriquecido | Quill v1 EOL | 🟡 |
| angular-calendar | ^0.31.0 | Componente calendario | Vigente | 🟢 |
| Lodash | ^4.17.21 | Utilidades JS | Mantenimiento | 🟡 |
| jQuery | ^3.6.1 | DOM manipulation (legacy) | Vigente pero innecesario en Angular | 🔴 |
| ng2-file-upload | ^5.0.0 | Upload de archivos | Mantenimiento bajo | 🟡 |
| ngx-pagination | ^6.0.2 | Paginación | Vigente | 🟢 |
| file-saver | ^2.0.5 | Descarga de archivos | Vigente | 🟢 |
| highlight.js | ^11.6.0 | Syntax highlighting | Vigente | 🟢 |
| perfect-scrollbar | ^1.5.5 | Scrollbar customizado | ⚠️ Sin mantenimiento activo | 🟡 |
| ng-recaptcha | 12.0.2 | Google reCAPTCHA | Versión desactualizada | 🟡 |
| TypeScript | ~5.1.0 | Tipado estático | Vigente | 🟢 |
| Webpack (custom) | — | Build personalizado (`webpack.config.js`) | ⚠️ Config custom sobre Angular CLI | 🟡 |

> [!warning] `@angular/flex-layout`
> Esta librería fue oficialmente deprecada por el equipo de Angular. Se recomienda migrar a CSS Grid / Flexbox nativo o Tailwind.

> [!warning] jQuery en Angular
> La presencia de jQuery junto a Angular es una señal de deuda técnica. Los módulos que lo usan deben ser identificados y refactorizados.
