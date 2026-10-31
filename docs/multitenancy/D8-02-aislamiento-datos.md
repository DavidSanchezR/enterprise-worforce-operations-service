# D8-02 — Estrategia de aislamiento de datos

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Aprobada

---


- **Estado:** aprobada.
- **Contexto:** el tenant es un grupo empresarial (R8-06), sin vínculos de datos entre tenants (R8-05), con escala desconocida (R8-04) y soporte con lectura permanente (R8-15).
- **Distinción de términos:** *aislamiento lógico* es la separación por reglas de software dentro de una misma base; *separación física de BD* son bases distintas en el mismo servidor o clúster; *separación de infraestructura* son servidores, red y despliegue distintos.

| Criterio | 1. BD compartida, aislamiento lógico | 2. BD por tenant | 3. Infraestructura dedicada |
|---|---|---|---|
| Aislamiento | Depende por completo de controles de software correctos | Una consulta mal filtrada no cruza de BD; app y servidor compartidos | Máximo; separa también cómputo, red y fallos |
| Costo | Más bajo por tenant | Medio; crece con el número de BD | Más alto, con piso fijo por cliente |
| Migraciones | Una sola vez | N bases; requiere orquestación y manejo de fallos parciales | N instalaciones; además despliegue de aplicación |
| Backup y restore | Restaurar un tenant exige extracción selectiva | Directo por tenant | Directo e independiente |
| Reporting consolidado | Sencillo entre tenants | Exige federar o replicar | Complejo si se necesita |
| Auditoría y vecino ruidoso | Carga compartida; auditoría por tenant se filtra | Menos interferencia de datos; cómputo compartido | Sin interferencia |
| Encaje | SaaS | SaaS o intermedio | Clientes que exijan infraestructura propia |

- **Decisión:** aislamiento lógico estricto obligatorio como línea base en todas las formas de despliegue, con defensa en profundidad. No se decide todavía entre BD compartida y BD por tenant para el SaaS: se exige que un tenant pueda ubicarse en BD compartida, BD propia o instalación dedicada sin cambios de aplicación. La separación entre mandantes, sedes y contratistas del mismo tenant se resuelve con autorización por alcance, no con aislamiento de datos.
- **Controles necesarios (un `TenantId` por sí solo no basta):**
  - *Aplicación:* contexto de tenant derivado de la identidad autenticada (usuario o dispositivo registrado), obligatorio en cada solicitud, proceso en segundo plano e ingestión de eventos, y falla cerrada si falta.
  - *Acceso a datos:* filtrado global por tenant, escrituras que fijan el tenant de forma automática, rechazo de referencias entre tenants y, si el motor lo permite, refuerzo en la propia BD.
  - *Autorización:* validar que el recurso pertenece al tenant y que el usuario tiene permiso y alcance.
  - *Transversales:* caché, colas, archivos, logs y búsquedas con clave por tenant, y pruebas automáticas de acceso cruzado.
- **Alternativas descartadas por el momento:** confiar solo en un `TenantId`; BD compartida sin capas adicionales.
- **Justificación:** la escala es desconocida; evita comprometerse con un costo operativo antes de tener datos.
- **Consecuencias arquitectónicas:** el acceso a datos no asume un único origen; las restricciones de unicidad se acotan al tenant; el acceso del soporte es un mecanismo separado y auditado.
- **Riesgos y mitigaciones:** diferir la elección puede complicar migraciones futuras (requisito de ubicación portable y criterios de decisión tempranos); un fallo de filtrado expone a otros tenants (capas redundantes y pruebas).
- **Preguntas pendientes:** escala real; volumen de eventos; motor de BD; restauración selectiva por tenant; reporting consolidado del proveedor, si existe.
