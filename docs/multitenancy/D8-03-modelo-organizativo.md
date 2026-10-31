# D8-03 — Modelo organizativo y jerarquía

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Aprobada

---


- **Estado:** aprobada.
- **Contexto:** distinguir tenant, mandante, contratista, persona, identidad operativa, usuario, sede, área, suscripción y módulos sin tratarlos como equivalentes (§4, §8.2 anterior).
- **Alternativas evaluadas:** árbol estricto (tenant → mandante → sede → área) frente a entidades con relaciones explícitas. El árbol se descarta porque las sedes pertenecen al grupo y mandantes y contratistas se relacionan de forma muchos a muchos (R8-07, R8-08).
- **Decisión:**

| Concepto | Qué es | Relaciones y restricciones | Origen |
|---|---|---|---|
| Tenant | Grupo empresarial que contrata; frontera dura de datos y de licencia | Contiene todo lo demás; sin vínculos con otros tenants | R8-05, R8-06 |
| Empresa mandante | Empresa del grupo que opera en sedes | Opera en una o más sedes; se relaciona con varias contratistas | R8-06 a R8-08 |
| Contratista | Un solo registro por tenant | Se relaciona con varias mandantes | R8-07 |
| Relación mandante–contratista | Contrato o alcance entre ambas | Los datos que dependen de la mandante pertenecen a la relación | R8-07 |
| Sede | Pertenece al grupo, no a una mandante | Las mandantes operan en ella; puede tener varias | R8-08 |
| Área o punto operativo | Parte de una sede; sede de configuración funcional | Pertenencia a una sede **no confirmada explícitamente** | Inferido |
| Persona | Individuo real; un registro por tenant | Unicidad dentro del tenant, no global | R8-05 |
| Vínculo | Relación de la persona con un empleador (mandante o contratista) | Solo uno activo a la vez, con historial | R8-16 |
| Identidad operativa | Persona en un vínculo | Una por vínculo; las credenciales cuelgan de ella; los eventos la referencian | R8-17 |
| Usuario de plataforma | Cuenta para iniciar sesión (grupo, contratista, soporte) | No equivale a persona; alcance por mandante, sede y contratista | R8-14 |
| Suscripción o contrato | Acuerdo a nivel de tenant | Fija módulos, límites, gracia y plazos de retención | R8-09, R8-10 |
| Módulos y capacidades | Lo contratado | Solo a nivel de tenant | R8-09 |

- **Justificación:** respeta las distinciones del documento y las respuestas del usuario; evita que "empresa" sirva para tres cosas distintas.
- **Consecuencias arquitectónicas:** la autorización por alcance debe expresar combinaciones de mandante, sede y contratista; vínculo e identidad operativa son entidades con ciclo de vida; unicidades acotadas al tenant; el contexto vigente al evento se conserva en cada registro (R8-48).
- **Riesgos y mitigaciones:** un modelo de alcances expresivo es difícil de probar (pocos tipos de alcance y pruebas de acceso cruzado); fuga entre mandantes a través de una contratista compartida (definir qué ve cada mandante); modelar de más (mantener fuera lo no confirmado).
- **Preguntas pendientes:** a qué pertenece un área; qué ve cada mandante de una contratista compartida; relación usuario-persona; si una contratista puede operar en una sede sin relación con una mandante concreta; clave de unicidad de la persona dentro del tenant; si un usuario de contratista tiene una cuenta por tenant o una cuenta con varias membresías.
