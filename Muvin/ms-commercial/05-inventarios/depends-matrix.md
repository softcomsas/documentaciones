# Matriz de Dependencias

**Última actualización:** 2026-04-27

```
                 │ Core │Contr│Valid│ PrS │
─────────────────┼──────┼─────┼─────┼─────┤
AppModule        │  ✓   │  ✓  │  ✓  │  -  │
CoreModule       │  -   │  -  │  -  │  ✓  │
ContractsModule  │  ✓   │  -  │  -  │  -  │
ValidationModule │  ✓   │  -  │  -  │  -  │
─────────────────┴──────┴─────┴─────┴─────┘

Core = CoreModule
Contr = ContractsModule
Valid = ValidationModule
PrS = PrismaService
```

**Leyenda:**
- ✓ Depende de
- ─ No depende

---

## Documentos Relacionados

- [[cross-module-dependencies|Dependencias Entre Módulos]] - Gráfico visual
- [[tree-estructura-archivos|Árbol de Archivos]] - Ubicación de código

