# D8-04 — Entitlement, autorización y configuración funcional

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Aprobada

---


- **Estado:** aprobada.
- **Contexto:** impedir que un tenant use módulos no contratados aunque conozca los endpoints, sin reducir el licenciamiento a una condición visual del frontend (§4, §10).
- **Alternativas evaluadas:** licencia como feature flag de frontend (descartada); fusionar licencia y permisos en un solo mecanismo (descartada); tres mecanismos separados validados en backend (adoptada).
- **Decisión:**

| Mecanismo | Responde a | Nivel | Reglas |
|---|---|---|---|
| Entitlement | ¿Qué módulos y límites tiene el tenant? | Tenant (R8-09) | Módulos y límites de cantidad (R8-10); ciclo de vida descrito abajo |
| Autorización | ¿Qué puede hacer este usuario aquí? | Rol (permisos) por alcance | Roles base y personalizados (R8-18); alcance por mandante, sede y contratista; los permisos se filtran por entitlement |
| Configuración funcional | ¿Qué reglas y servicios están activos? | Cadena área, sede, mandante, tenant (R8-31) | Solo para módulos contratados; la mandante aplica a reglas de sus personas (R8-24); el sistema explica de qué nivel salió cada valor |

- **Orden de comprobación en backend (inferido):** identidad autenticada, contexto de tenant, entitlement del módulo, permiso y alcance, configuración y reglas de dominio. Debe cubrir todas las vías: API, procesos en segundo plano, ingestión de eventos y componente local.
- **Clasificación de operaciones (cuatro clases):** *amplía*, *reduce*, *neutra* y *consulta o exportación de histórico*. La licencia bloquea las que amplían; las que reducen y la consulta de histórico siguen permitidas, siempre sujetas a autorización (R8-13, R8-21). Cada caso de uso declara su clase y los permisos que exige; sin declaración se deniega.
- **Ciclo de vida del entitlement (nombres provisionales):** activo; gestión bloqueada (sin altas ni cambios que amplían); eventos restringidos tras la gracia. Es uniforme para los módulos con eventos (R8-22). En eventos restringidos, el acceso, la asistencia y el comedor siguen operando con reglas cargadas y los eventos nuevos van a **cuarentena** por tenant, módulo y tipo de evento, sin descartarse (R8-12, R8-53).
- **Gracia:** atributo de la suscripción, gestionado solo por roles de plataforma; con vencimiento explícito; tope máximo **acumulado por episodio de restricción, sin excepción** (R8-23, R8-29, R8-30). El episodio se cierra solo tras un período sin incidencias (R8-34). Motivo obligatorio, doble aprobación y aviso al tenant **no** fueron exigidos.
- **Límites:** el contador de personas activas cuenta vínculos no cerrados, incluidos los suspendidos (R8-26, R8-32). Reservar cupo es atómico al activar un vínculo.
- **Suspensión:** estados separados por entidad (acreditación, credencial, vínculo) que se combinan al decidir (R8-37).
- **Histórico, retención y eliminación:** lectura y exportación siempre disponibles dentro del plazo de conservación (R8-21, R8-27). Al terminar el contrato rige un plazo máximo parametrizable; antes de devolver o eliminar, el aviso es obligatorio y requiere respuesta explícita; sin respuesta los datos quedan bloqueados (R8-41 a R8-43). Para una persona: bloqueo y eliminación al vencer el plazo, que corre desde el cierre del vínculo; la solicitud con vínculo abierto se pospone (R8-36, R8-38 a R8-40).
- **Soporte del proveedor:** lectura permanente auditada y cambios con autorización (R8-15). Exigen autorización del tenant: configuración funcional de área, reparación con script técnico, restablecer acceso de un usuario, modificar roles o permisos, mover o restaurar el tenant y extraer datos (R8-35, R8-44). Basta aprobación interna para corregir o reprocesar eventos y modificar identidad operativa o credencial (R8-35). Los autorizadores los designa el tenant y, sin respuesta, no se hace el cambio (R8-33). Designarlos es recomendado, no obligatorio (R8-45).
- **Auditoría:** cambios de entitlements, límites, roles, configuración, gracia, accesos y cambios del soporte, bloqueos y eliminaciones, con quién, cuándo y valores antes y después.
- **Justificación:** mantiene la separación exigida por el documento y hace comprobable cada control.
- **Consecuencias arquitectónicas:** un punto único de evaluación de entitlements; catálogo de permisos por módulo; modelo de alcances; configuración jerárquica con resolución explicable; replicación al borde de un subconjunto acotado.
- **Riesgos y mitigaciones:**
  - El soporte puede alterar eventos y credenciales sin aprobación del tenant, con la auditoría como única traza (R8-35). Mitigaciones posibles no adoptadas: auditoría inmutable consultable por el tenant, o notificación posterior.
  - Lectura permanente del soporte frente a datos sensibles y datos bloqueados.
  - Sin autorizador designado el soporte no puede hacer cambios autorizados; sin contacto de avisos los datos de un tenant nunca se eliminarían automáticamente, y R8-42 puede producir conservación indefinida.
  - Controles olvidados en algún punto de entrada (verificación obligatoria por defecto y pruebas por endpoint); escalada de privilegios en roles personalizados (no se concede lo que no se tiene).
- **Preguntas pendientes:** valores del plazo de conservación, del plazo por defecto por persona, del tope de gracia y del período sin incidencias; anticipación del aviso previo; qué ocurre con una persona activa más allá de lo definido; catálogo completo de cambios del soporte; cómo se concede la autorización si no hay usuario del tenant disponible; plazos de eliminación en copias de seguridad; desempate de consumos; definición exacta de la suspensión por entidad.
