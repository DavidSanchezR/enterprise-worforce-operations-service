## 8. Multi-tenancy, licencias y aislamiento de datos

> **Estado de este punto:** decisiones D8-01 a D8-05 **aprobadas por el usuario** en la sesión de continuación del punto 8. Esta sección reemplaza el contenido anterior del punto 8 ("pendiente de decisión formal") y conserva la terminología del resto del documento.
>
> **Convención de certeza usada en esta sección:**
> - **Declarado:** requisito o regla indicada por el usuario en la sesión (identificado como `R8-nn`).
> - **Inferido:** deducción técnica de quien redactó, derivada de lo declarado; requiere validación si no figura en una decisión aprobada.
> - **Verificado externo:** dato contrastado en una fuente pública citada; no sustituye asesoría legal.
> - **Pendiente:** pregunta abierta; no debe tratarse como decidida.
>
> **Alcance:** solo decisiones de multi-tenancy, licenciamiento y aislamiento. No define proyectos .NET, tecnologías, tablas ni código. El stack sigue pendiente (§12, ítem 4).

### 8.1. Resumen de decisiones aprobadas

| ID | Tema | Estado | Idea central |
|---|---|---|---|
| D8-01 | Modelo de despliegue | Aprobada | Un producto, una base de código, agnóstico al despliegue: plano central SaaS multi-tenant o dedicado (un tenant), ambos operados por el proveedor, más un componente local en sede solo donde haga falta |
| D8-02 | Estrategia de aislamiento de datos | Aprobada | Aislamiento lógico estricto como línea base en todos los despliegues; ubicación portable del tenant (BD compartida, BD propia o instalación dedicada) sin cambiar la aplicación |
| D8-03 | Modelo organizativo y jerarquía | Aprobada | Modelo de entidades con relaciones explícitas, no un árbol; el tenant es el grupo empresarial |
| D8-04 | Entitlement, autorización y configuración funcional | Aprobada | Tres mecanismos separados, validados en backend; ciclo de vida de licencia con gracia, cuarentena y retención |
| D8-05 | Implicaciones para el monolito modular | Aprobada | Responsabilidades y límites de contexto de tenant, autorización, propiedad de datos, contratos, transacciones, auditoría y pruebas |

### 8.2. Requisitos declarados por el usuario

Cada requisito es una entrada de las decisiones. Los marcados "sin verificar" dependen de comprobación externa.

| ID | Requisito declarado |
|---|---|
| R8-01 | El control de acceso físico debe seguir autorizando o denegando accesos localmente cuando se pierde el enlace con la plataforma central |
| R8-02 | Habrá clientes que acepten datos en una nube operada por el proveedor y otros que exijan infraestructura propia |
| R8-03 | El proveedor opera y actualiza también las instalaciones dedicadas |
| R8-04 | La cantidad y el tamaño de los tenants del SaaS no están definidos |
| R8-05 | Una contratista o persona puede trabajar para varios tenants, con registro separado en cada uno y sin compartir información entre ellos |
| R8-06 | El tenant es un grupo empresarial que contrata como un solo cliente y puede contener varias empresas mandantes |
| R8-07 | Dentro de un tenant, la contratista es un solo registro que se relaciona con varias mandantes, cada relación con su propio contrato o alcance |
| R8-08 | La sede pertenece al grupo (tenant); las mandantes operan en ella sin ser sus dueñas |
| R8-09 | Los módulos se contratan y habilitan solo a nivel de tenant |
| R8-10 | La licencia limita módulos y también cantidades (personas activas, sedes, terminales) |
| R8-11 | Ante exceso de límite, impago o desactivación: se bloquean altas y cambios, hay período de gracia para eventos y después también se restringen los eventos |
| R8-12 | Tras la gracia, el acceso físico sigue autorizando con las reglas ya cargadas y los eventos nuevos se guardan en cuarentena sin procesar ni consultar |
| R8-13 | Bajas, suspensiones y revocaciones están siempre permitidas, incluso con la licencia restringida |
| R8-14 | Tienen usuario el personal del grupo (mandantes), el personal de las contratistas y el soporte del proveedor. Los trabajadores en autoservicio quedan fuera de alcance por ahora |
| R8-15 | El soporte del proveedor puede consultar datos de negocio de un tenant de forma permanente; modificarlos requiere autorización |
| R8-16 | Una persona tiene como máximo un vínculo laboral activo a la vez, con historial en el mismo registro |
| R8-17 | La identidad operativa se crea por cada vínculo persona-empleador |
| R8-18 | El producto trae roles base y cada tenant puede crear o ajustar roles propios |
| R8-19 | La configuración funcional se aplica a nivel de área o punto operativo |
| R8-20 | La configuración admite valores por defecto heredables desde niveles superiores, sobrescribibles en el área |
| R8-21 | Con un módulo restringido o desactivado, el tenant conserva lectura y exportación de su histórico, dentro del plazo de conservación de R8-27 |
| R8-22 | Asistencia y comedor se comportan como el acceso físico ante una licencia restringida (siguen operando con reglas cargadas; eventos nuevos a cuarentena) |
| R8-23 | La duración de la gracia la fija o extiende el proveedor caso a caso |
| R8-24 | La mandante puede definir configuración que aplica a las reglas de sus personas (no a las áreas) |
| R8-25 | Los cambios de datos de negocio del soporte exigen autorización del tenant; los técnicos o de plataforma, solo aprobación interna del proveedor |
| R8-26 | El límite de personas activas cuenta los vínculos activos, hayan usado o no el sistema |
| R8-27 | Tras terminar el contrato, la conservación tiene un plazo máximo parametrizable por contrato; al vencer se devuelven o eliminan los datos, salvo solicitud del cliente o norma que exija más. El valor se fija con asesoría legal |
| R8-28 | Las reglas del lugar (sede/área) mandan sobre la mandante (matizada por R8-31) |
| R8-29 | La gracia tiene un tope máximo de duración |
| R8-30 | El tope es acumulado por episodio de restricción, sin excepción |
| R8-31 | Orden de resolución de configuración: área, sede, mandante, tenant |
| R8-32 | Una persona suspendida sin cierre de vínculo sigue ocupando cupo del límite |
| R8-33 | El tenant designa por adelantado autorizadores (con respaldo) para cambios del soporte; sin respuesta no se hace el cambio |
| R8-34 | El episodio de restricción no se cierra al regularizar, sino tras un período sin incidencias |
| R8-35 | Exigen autorización del tenant: cambiar la configuración funcional de un área y reparar datos con un script técnico. Basta aprobación interna para: corregir o reprocesar eventos y modificar identidad operativa o credencial |
| R8-36 | Ante la solicitud de eliminar los datos de una persona, sus datos se bloquean y se eliminan al vencer el plazo |
| R8-37 | La suspensión se maneja con estados separados por entidad: acreditación (elegibilidad), credencial y vínculo |
| R8-38 | Si la persona tiene el vínculo abierto al pedir el bloqueo, este se pospone hasta cerrar el vínculo |
| R8-39 | Con solicitud de la persona rige un plazo tope desde la solicitud; sin solicitud, un plazo tope por defecto configurable (días o meses) |
| R8-40 | Con el vínculo abierto, el plazo corre desde que se cierra el vínculo |
| R8-41 | Antes de devolver o eliminar los datos de un tenant por vencimiento del plazo, el aviso es obligatorio, con anticipación definida, a los contactos designados del tenant |
| R8-42 | Si el tenant no responde al aviso, los datos quedan bloqueados hasta que responda, sin eliminación automática |
| R8-43 | Un aviso solo se considera confirmado con una respuesta explícita de un contacto designado |
| R8-44 | Restablecer acceso de un usuario, modificar roles o permisos, mover o restaurar el tenant y extraer datos para diagnóstico exigen autorización del tenant |
| R8-45 | Designar autorizadores de soporte y contactos de avisos es recomendado, no obligatorio, al dar de alta un tenant |
| R8-46 | Las operaciones que cruzan módulos confirman o fallan juntas en una sola transacción coordinada |
| R8-47 | Un núcleo organizativo único es dueño de organizaciones, sedes, áreas, personas, vínculos e identidades operativas |
| R8-48 | Eventos y registros de los módulos guardan las claves de contexto vigentes al ocurrir (sede, mandante, contratista, identidad operativa) |
| R8-49 | Toda operación auditada confirma junto con su registro de auditoría; si este falla, la operación no se ejecuta |
| R8-50 | Los terminales IDEMIA y ZKTeco tienen memoria o interfaz local para permisos y validación (**sin verificar** por modelo y firmware; HID no se mencionó) |
| R8-51 | En acceso y asistencia la validación offline es simple; en comedor, la restricción por rango de horas y por comedor no se valida offline y el consumo queda como registro observado, con notificación de exceso y posible sanción o costo según regla |
| R8-52 | El enrolamiento biométrico es central, no en el equipo |
| R8-53 | Todos los eventos y consumos deben registrarse en el sistema |

### 8.3. D8-01 — Modelo de despliegue

- **Estado:** aprobada.
- **Contexto:** servir a clientes que aceptan nube y a otros que exigen infraestructura propia (R8-02), con operación de acceso físico sin conexión (R8-01) y con el proveedor como operador de todas las instalaciones (R8-03).
- **Alternativas evaluadas:** SaaS centralizado puro; instalación dedicada para todos; híbrido, entendido como dos ejes independientes (plano central SaaS o dedicado, y componente local en sede).
- **Decisión:** un solo producto y una sola base de código, agnóstico al despliegue, con tres formas: plano central SaaS multi-tenant operado por el proveedor; plano central dedicado (un tenant, infraestructura exigida por el cliente) operado por el proveedor; y un componente local en sede solo donde los terminales no sean autónomos. El componente local es una unidad de despliegue, no un microservicio.
- **Alternativas descartadas por el momento:** SaaS puro como única oferta (no cubre R8-01 ni R8-02); dedicado para todos (costo sin necesidad); instalación operada por el cliente (fuera de alcance por R8-03).
- **Justificación:** es la única combinación que cubre los requisitos declarados sin duplicar el producto.
- **Consecuencias arquitectónicas:** contexto de tenant válido para uno o muchos tenants; migraciones aplicables a N instalaciones; replicación al borde de un subconjunto de datos de autorización; licencia verificable sin conexión; actualización remota de componentes en sede.
- **Riesgos y mitigaciones:** divergencia entre formas de despliegue (automatización y pruebas en todas); costo operativo de N instalaciones dedicadas (criterio comercial por definir); sedes con conectividad limitada para actualizar (política de versiones por definir).
- **Preguntas pendientes:** autonomía real por modelo de terminal; obsolescencia tolerada en revocaciones; duración máxima de autonomía local; requisitos regulatorios y de residencia de datos; si hace falta un componente intermedio en sede para comunicación y reenvío de eventos.

### 8.4. D8-02 — Estrategia de aislamiento de datos

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

### 8.5. D8-03 — Modelo organizativo y jerarquía

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

### 8.6. D8-04 — Entitlement, autorización y configuración funcional

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

### 8.7. D8-05 — Implicaciones para el monolito modular

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

### 8.8. Marco legal peruano consultado (no es asesoría legal)

Consulta realizada a petición del usuario sobre eliminación y conservación de datos personales, limitada al mercado peruano.

- **Verificado externo** en el texto del [D.S. 016-2024-JUS](https://www.congreso.gob.pe/Docs/DGP/DIDP/files/ds_016-2024-jus.pdf) (nuevo reglamento de la Ley 29733); entrada en vigor el 31 de marzo de 2025 según [EY Perú](https://www.ey.com/es_pe/insights/law/proteccion-datos-personales-peru-nuevo-reglamento):
  - Existe el derecho de cancelación o supresión (eliminar los datos); plazo de respuesta de diez días y denegatoria justificada (arts. 69.3 y 73).
  - El *bloqueo* reserva los datos, impide su tratamiento incluida la visualización, y puede durar el tiempo necesario para determinar responsabilidades durante el plazo de prescripción; es paso previo a la cancelación (art. III).
  - Para el **encargado del tratamiento**, la conservación es como máximo de dos años desde la finalización del último encargo, y la conservación indeterminada está prohibida por regla general salvo norma expresa que exija más (art. 31.2); debe garantizar y evidenciar la supresión o destrucción al terminar el servicio (arts. 29.5 y 30.4).
  - Se debe informar al titular el plazo de conservación (art. 6.1). Los datos biométricos son datos sensibles y su tratamiento exige consentimiento por escrito (art. III y art. 8).
- **No verificado:** el texto de las excepciones a la supresión (art. 14 de la Ley); si el proveedor es *encargado* o *responsable* del tratamiento en cada caso (es una inferencia); si alguna norma laboral, tributaria o sectorial exige conservar ciertos datos más tiempo.
- **Tensiones con las decisiones:** R8-42 (bloqueo sin eliminación hasta respuesta del tenant) puede producir conservación indefinida, y el plazo máximo de R8-27 debe contrastarse con el límite de dos años del encargado. Ambos puntos requieren validación con un abogado especialista antes de fijar valores o cláusulas contractuales.

### 8.9. Riesgos consolidados

1. Fuga entre tenants por un fallo de filtrado: capas redundantes y pruebas automáticas de acceso cruzado.
2. Lectura permanente del soporte, y cambios sobre eventos y credenciales sin aprobación del tenant (R8-15, R8-35).
3. Datos biométricos centralizados (R8-52): máxima sensibilidad, eliminación en terminales con evidencia.
4. Conservación indefinida posible (R8-42, R8-45) frente al marco legal (§8.8).
5. Divergencia entre formas de despliegue y costo operativo de instalaciones dedicadas.
6. Obsolescencia de revocaciones en terminales desconectados.
7. Acoplamiento por transacción única entre módulos y núcleo organizativo como dependencia de todos.
8. Pérdida de eventos en terminales con memoria llena o desconectados mucho tiempo, frente a R8-53.
9. Tope de gracia sin excepción que obliga a restringir aun en disputas comerciales (R8-30).

### 8.10. Preguntas abiertas consolidadas

**Negocio y legal:** valores del plazo de conservación, del plazo por defecto por persona, del tope de gracia y del período sin incidencias; anticipación del aviso previo; rol de encargado o responsable y normas de conservación; consentimiento biométrico; escala y volumen de eventos; modelo comercial y cobro por sede si se quisiera.

**Modelo y producto:** pertenencia de áreas; qué ve cada mandante de una contratista compartida; relación usuario-persona y cuentas con varios tenants; contratista sin mandante concreta; clave de unicidad de la persona; definición de la suspensión por entidad; catálogo completo de cambios del soporte; autorización sin usuario del tenant disponible; persona activa que pide bloqueo más allá de R8-38.

**Arquitectura:** frontera núcleo organizativo e Identity & Credential Management; auditoría de plataforma frente a la del tenant; conciliación del contexto de eventos del borde; desempate de consumos; dispositivos no registrados; granularidad de Platform; stack; componente intermedio en sede.

**Terminales:** capacidades reales por modelo y firmware (IDEMIA, ZKTeco, HID); búfer de eventos; obsolescencia tolerada en revocaciones; origen y formato de las plantillas por fabricante.

### 8.11. Impacto en otras secciones del documento (sin modificarlas)

- **§6 (Platform):** Tenancy, Authorization, Licensing, Audit y Configuration quedan con responsabilidades definidas en §8.7. Se añade el **núcleo organizativo** como concepto de dependencia común; su ubicación y granularidad de proyectos siguen sin decidir.
- **§9 (terminales):** se deben reflejar R8-50 a R8-53 (autonomía hipotética, enrolamiento central, consumo observado, registro de todos los eventos) y la prohibición de acceso directo a la BD.
- **§12 (decisiones pendientes):** los ítems 1, 2, 3 y 6 quedan decididos o acotados; el 15 queda parcialmente definido (módulos, límites, gracia, retención); persisten el 4, 13, 14 y 16 a 20.
- **Terminología:** *tenant* = grupo empresarial; *mandante*, *contratista*, *sede* y *área* son entidades distintas dentro de él; *identidad operativa* = persona en un vínculo.
