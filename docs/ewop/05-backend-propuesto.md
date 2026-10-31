# EWOP — Arquitectura de backend propuesta (§6)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Boceto conceptual. Las responsabilidades de Platform se detallan en multitenancy/D8-05

---


La siguiente estructura es un **boceto conceptual**, no una instrucción de crear todos los proyectos de inmediato:

```text
src/
├── Host/
│   └── Enterprise.WorkforcePlatform.Api/
├── BuildingBlocks/
│   ├── Enterprise.SharedKernel/
│   └── Enterprise.Contracts/
├── Modules/
│   ├── Accreditation/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   ├── IdentityCredentials/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   ├── PhysicalAccess/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   ├── TimeAttendance/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   └── DiningConsumption/
│       ├── Domain/
│       ├── Application/
│       └── Infrastructure/
└── Platform/
    ├── Tenancy/
    ├── Authorization/
    ├── Licensing/
    ├── Audit/
    └── Configuration/
```

### Responsabilidades por capa

- **Domain:** entidades, objetos de valor, reglas e invariantes del dominio. No debe depender de ASP.NET Core, EF Core ni infraestructura.
- **Application:** casos de uso y orquestación; depende del dominio y de abstracciones necesarias.
- **Infrastructure:** persistencia, acceso a sistemas externos, adaptadores y detalles técnicos.
- **API/Host:** punto de entrada y composición del sistema. No debe convertirse en el lugar donde se acumulen las reglas de negocio.
- **Contracts:** contratos de comunicación necesarios entre módulos o integraciones. Deben mantenerse estables y mínimos.
- **SharedKernel:** solo conceptos genuinamente comunes y estables; mantenerlo reducido.
- **Platform:** capacidades técnicas/operativas transversales, con límites claros. Debe evaluarse si cada área merece un proyecto independiente o si algunas son solo componentes dentro del host al principio.

### Dependencias esperadas

Dirección general:
- `Domain` no depende de `Application`, `Infrastructure` ni del framework web.
- `Application` depende de `Domain` y de abstracciones.
- `Infrastructure` implementa abstracciones usadas por `Application`.
- `API/Host` conecta dependencias e invoca casos de uso.
- Un módulo no debe saltarse sus contratos para acceder a las tablas/repositorios internos de otro módulo.

El grafo exacto de referencias de proyectos debe diseñarse y validarse antes de implementar.
