# EWOP — Conceptos y reglas de dominio (§4)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Vigente. El punto 8 amplía las definiciones de tenant, mandante, contratista, sede y entitlement (ver multitenancy/D8-03)

---


Evitar mezclar los siguientes conceptos:

| Concepto | Significado que debe conservarse |
|---|---|
| Persona | Individuo real, por ejemplo un trabajador propio o contratista. |
| Identidad operativa | Representación de esa persona en el contexto de la plataforma o de una organización. |
| Credencial | Medio o mecanismo utilizado para identificar a una persona, por ejemplo tarjeta, QR u otro identificador admitido. |
| Identificador tecnológico | Valor/protocolo/ID específico de una tarjeta, terminal, proveedor o sistema. No equivale por sí solo a una identidad. |
| Acreditación/elegibilidad | Resultado de validar requisitos documentarios u operativos. No equivale a autorización universal. |
| Autorización | Decisión de permitir una operación concreta según las reglas, el usuario, la identidad, el recurso y el contexto. |
| Evento de acceso | Intento o resultado de una operación de acceso físico. |
| Marcación de asistencia | Evento capturado para procesar asistencia/jornada. |
| Evento de consumo | Registro de una prestación o consumo en comedor. |
| Tenant/cliente de plataforma | Límite de cliente/organización para configuración, datos y módulos contratados; debe definirse con precisión. |
| Empresa/mandante/principal | Organización con un rol de negocio específico, que no debe confundirse automáticamente con el tenant. |
| Contratista | Empresa o entidad contratista, con relaciones de negocio que deben modelarse explícitamente. |
| Instalación/sede | Lugar operativo que puede pertenecer a un cliente y contener áreas, puertas, terminales o comedores. |
| Entitlement/licencia | Módulo o capacidad que el cliente tiene contratada. |
| Permiso de usuario | Operaciones que un usuario está autorizado a ejecutar. |

### Reglas de diseño derivadas

1. **La acreditación no es lo mismo que la autorización.** Estar acreditado no significa que una persona pueda entrar a cualquier área o consumir cualquier servicio.
2. **La licencia no es lo mismo que el permiso de usuario.** Tener contratado el módulo no concede automáticamente acceso a todas sus funciones.
3. **La visibilidad del menú no es una medida de seguridad.** La API/backend debe validar el entitlement del tenant y la autorización del usuario.
4. **Los eventos tienen semánticas distintas.** Acceso, marcación de asistencia y consumo no deben reducirse a un único tipo genérico de evento sin conservar su significado.
5. **La identidad no debe acoplarse al fabricante.** Los IDs y protocolos específicos de terminales deben quedar en adaptadores de integración.
6. **No asumir que se necesita almacenar plantillas biométricas centralmente.** La estrategia de biometría y privacidad debe definirse a partir de capacidades reales, riesgos y requisitos aplicables.
7. **El aislamiento multi-tenant no se garantiza solo con incluir un TenantId en las tablas.** Se requieren controles de acceso y validación consistentes en las capas correspondientes.
