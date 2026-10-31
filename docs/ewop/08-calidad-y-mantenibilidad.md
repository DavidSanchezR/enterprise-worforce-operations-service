# EWOP — Requisitos de calidad y mantenibilidad (§10)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Vigente

---


Objetivos expresos de diseño:
- Alta mantenibilidad.
- Bajo acoplamiento.
- Alta cohesión.
- Capacidad de incorporar módulos y proveedores nuevos.
- Una base de producto común para clientes con contratos distintos.
- Seguridad consistente en backend.
- Trazabilidad de operaciones y eventos.
- Límites de dominio explícitos.
- Posibilidad de evolución hacia servicios independientes cuando esté justificada.

### Reglas prácticas propuestas

1. No añadir abstracciones sin una responsabilidad clara.
2. No permitir que un módulo escriba directamente en las tablas de otro módulo.
3. Evitar un `SharedKernel` excesivamente grande.
4. Separar reglas de negocio de ASP.NET Core, EF Core y SDK de fabricantes.
5. Diseñar contratos de integración con versionado y semántica clara.
6. Separar la captura de eventos de su interpretación funcional.
7. No usar menús o feature flags del frontend como controles de seguridad.
8. Probar explícitamente aislamiento entre tenants, entitlements y permisos.
9. Definir decisiones de arquitectura antes de crear una gran cantidad de proyectos y archivos.
10. No convertir automáticamente cada módulo en microservicio.

No se ha fijado todavía el stack completo del nuevo producto, las versiones definitivas de .NET, framework de frontend, ORM, proveedor de base de datos, mecanismo de identidad/autenticación ni infraestructura de despliegue para EWOP. Consultar el repositorio existente y al usuario antes de asumir que una decisión técnica del proyecto EAC se aplica automáticamente al producto ampliado.
