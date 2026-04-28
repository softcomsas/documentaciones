# Clasificación Funcional

**Última actualización:** 2026-04-27

## Por Responsabilidad

| Categoría | Archivos | Responsabilidad |
|-----------|----------|-----------------|
| **Data Access** | ContractsRepository, EntitiesRepository, ProductsRepository, KeysRepository | Queries a MySQL |
| **Business Logic** | ContractsService, ValidationService | Validaciones y orquestación |
| **HTTP/RPC** | ContractsController, ValidationController | Recibir y retornar RPC |
| **Infrastructure** | PrismaService, ConfigModule | BD y configuración |
| **Utilities** | api-response.ts, logger.ts, identity.ts | Helpers compartidas |
| **Contracts** | commercial/*, auth/*, integrations/*, logs/* | Interfaces inter-servicios |

---

## Por Tipo de Código

| Tipo | Cantidad | Ejemplos |
|------|----------|----------|
| Services | 2 | ContractsService, ValidationService |
| Controllers | 2 | ContractsController, ValidationController |
| Repositories | 4 | ContractsRepository, EntitiesRepository, ... |
| Modules | 3 | AppModule, CoreModule, ContractsModule, ValidationModule |
| Interfaces | 15+ | Distributed in contracts/ |
| DTOs | inline | Tipados en controllers |
| Guards/Pipes | - | No hay (validación en service) |

---

## Documentos Relacionados

- [[tree-estructura-archivos|Árbol de Archivos]] - Ubicación
- [[../01-modulos/_indice-modulos|Módulos]] - Organización

