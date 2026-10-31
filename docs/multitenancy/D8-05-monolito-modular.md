# D8-05 — Implicaciones para el monolito modular

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Aprobada. Describe responsabilidades y límites, no proyectos .NET

---


- **Estado:** aprobada. Describe responsabilidades y límites, no proyectos .NET ni tecnologías (§12, ítem 4).
- **Alternativas evaluadas:** consistencia entre módulos (transacción única, reserva y compensación, eventos con outbox); propiedad de datos organizativos (núcleo único, núcleo más Identidad, copias por módulo); filtro de alcance (contexto en cada registro, consulta al núcleo, mixto); auditoría (misma transacción, mixta, asíncrona); componente local (mismo código, implementación separada, autonomía del terminal).
- **Decisión:**

| Tema | Responsabilidad y límite | Origen |
|---|---|---|
| Contexto del tenant | Platform lo establece desde la identidad autenticada (usuario o dispositivo registrado), nunca desde un parámetro del cliente, y falla cerrado. Los módulos lo leen por una abstracción y no aceptan un tenant como argumento. El soporte usa una identidad de plataforma con tenant elegido explícitamente | D8-02, R8-15 |
| Autorización y entitlement | Un punto único evalúa tenant, entitlement, permiso y alcance, configuración. Cada caso de uso declara su clase de operación y permisos | D8-04 |
| Propiedad de datos | El núcleo organizativo es dueño de organizaciones, sedes, áreas, personas, vínculos e identidades operativas. Licenciamiento es dueño de suscripción, límites, gracia, episodios y retención. Cada módulo es dueño de sus eventos y registros, con el contexto del momento | R8-47, R8-48 |
| Acceso entre módulos | Solo por contratos versionados, mínimos y estables, sin leer tablas ajenas. Los contratos que participan en una operación conjunta pueden unirse a la transacción de quien llama. Terminales y adaptadores entran por un contrato de aplicación, nunca por acceso directo a la BD | R8-46, R8-50 |
| Persistencia y transacciones | Los módulos de un tenant comparten la base del tenant con propiedad separada. Una transacción coordinada para operaciones entre módulos del mismo tenant, nunca entre tenants. Ingestión desde el borde asíncrona, idempotente y con deduplicación | R8-46, D8-02 |
| Auditoría | Auditoría del tenant dentro de su base, atómica con la operación sensible. Auditoría de plataforma separada y fiable, no atómica con la anterior | R8-49 |
| Configuración | Un componente resuelve la cadena área, sede, mandante, tenant y explica el origen de cada valor. Cada módulo define sus parámetros. Un subconjunto acotado se replica a terminales y borde | R8-31 |
| Eventos | Se guarda el evento original; su evaluación (por ejemplo, consumo observado) es posterior y separada. Ningún evento se descarta: los que no encajan se registran con su resultado. Cada tipo conserva su semántica | R8-51, R8-53 |
| Datos biométricos | Enrolamiento central; plantillas bajo el dominio de credenciales; aislamiento por tenant; fuera de la lectura de soporte por defecto (recomendación); alcanzadas por el contrato de ciclo de vida, incluida la eliminación en terminales | R8-52 |
| Ciclo de vida de datos | Todo módulo implementa un contrato común para bloquear datos de una persona, exportar histórico y eliminar o devolver todos los datos del tenant, con evidencia | R8-27, R8-36, R8-41 |
| Borde y terminales | Autonomía de terminales donde sea viable (hipótesis por modelo); componente intermedio en sede solo si hace falta para comunicación y reenvío de eventos. La consulta online de un terminal pasa por un contrato, no por la BD | R8-50 |
| Pruebas de aislamiento | Acceso cruzado entre tenants, entre mandantes y contratistas; entitlement y permiso por endpoint; clase de operación declarada; ciclo de vida de datos por módulo; terminales sin acceso directo a BD | §10 |

- **Alternativas descartadas por el momento:** copias de referencia por módulo; auditoría asíncrona; microservicios por módulo, sin evidencia que lo justifique (§5.1).
- **Justificación:** mantiene la separación de responsabilidades, evita fugas entre tenants, simplifica la consistencia y respeta la propiedad de datos de §5.4.
- **Consecuencias y costos aceptados:** la transacción única encarece una futura extracción de módulos, que exigirá compensaciones o eventos; el núcleo organizativo es dependencia de todos y debe mantenerse pequeño; el contador de cupo es un punto caliente en cargas masivas.
- **Riesgos y mitigaciones:** acoplamiento por transacción (aceptado); núcleo como cuello de botella de cambios (contratos pequeños y versionados); terminales accediendo a BD (prohibido por contrato); eventos corregidos por el soporte sin aprobación del tenant (R8-35); pérdida de eventos en terminales desconectados mucho tiempo o con memoria llena (alertas y tamaño de búfer por modelo, sin verificar).
- **Preguntas pendientes:** frontera entre el núcleo organizativo e Identity & Credential Management (§3.2 asigna la identidad operativa a ese módulo); auditoría de plataforma frente a la del tenant cuando una acción toca dos bases; conciliación del contexto de eventos capturados sin conexión; reglas de desempate al conciliar consumos con relojes distintos; dónde retener eventos de dispositivos no registrados (no puede asignarse tenant); granularidad de los componentes de Platform (§6, sin decidir); stack.
