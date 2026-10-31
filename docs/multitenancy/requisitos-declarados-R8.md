# Punto 8 — Requisitos declarados (R8-01 a R8-53)

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Certeza:** Todo lo de este archivo es **declarado** por el usuario; R8-50 está sin verificar

---


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
