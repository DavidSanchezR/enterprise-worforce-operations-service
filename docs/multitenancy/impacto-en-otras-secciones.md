# Punto 8 — Impacto en otras secciones del documento de contexto

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Informativo; no modifica el contexto original

---


- **§6 (Platform):** Tenancy, Authorization, Licensing, Audit y Configuration quedan con responsabilidades definidas en §8.7. Se añade el **núcleo organizativo** como concepto de dependencia común; su ubicación y granularidad de proyectos siguen sin decidir.
- **§9 (terminales):** se deben reflejar R8-50 a R8-53 (autonomía hipotética, enrolamiento central, consumo observado, registro de todos los eventos) y la prohibición de acceso directo a la BD.
- **§12 (decisiones pendientes):** los ítems 1, 2, 3 y 6 quedan decididos o acotados; el 15 queda parcialmente definido (módulos, límites, gracia, retención); persisten el 4, 13, 14 y 16 a 20.
- **Terminología:** *tenant* = grupo empresarial; *mandante*, *contratista*, *sede* y *área* son entidades distintas dentro de él; *identidad operativa* = persona en un vínculo.
