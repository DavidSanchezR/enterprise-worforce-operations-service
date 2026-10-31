# EWOP — Resumen ejecutivo y objetivo del producto (§1 y §2)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Contexto de producto; distingue hechos confirmados, recomendaciones y pendientes

---


El proyecto partió como **Enterprise Access Control (EAC)**, una solución empresarial de control de acceso físico para personal propio y contratistas, orientada a entornos corporativos e industriales/mineros. El usuario quiere evolucionarla hacia una plataforma integrada que cubra el ciclo operativo del personal y pueda ofrecerse a clientes que contraten distintos módulos.

El nombre conceptual recomendado en la conversación fue **Enterprise Workforce Operations Platform (EWOP)**, traducido conceptualmente como **Plataforma Empresarial de Gestión Operativa del Personal**. Es una recomendación, no un nombre comercial definitivamente aprobado ni una marca cuya disponibilidad se haya verificado.

La dirección arquitectónica recomendada es comenzar con un **monolito modular**, dentro de un **monorepo**, con límites explícitos de dominio y contratos entre módulos. Los módulos funcionales no tienen que convertirse automáticamente en aplicaciones, procesos o microservicios separados. Los clientes pueden contratar distintos módulos mediante configuración de licencias/entitlements, sin mantener una variante del código por cliente.

El diseño debe preservar bajo acoplamiento, alta cohesión, mantenibilidad, seguridad multiempresa y capacidad de extraer servicios en el futuro cuando exista una necesidad operativa real.

### Estado de certeza

- **Contexto existente confirmado:** el proyecto EAC tiene un repositorio GitHub y una historia de trabajo con baseline, validación posterior y documentación final.
- **Dirección recomendada:** evolucionar EAC hacia EWOP con módulos adicionales y arquitectura de monolito modular.
- **Pendiente:** confirmar el modelo de despliegue, la estrategia de aislamiento de datos, los límites concretos de proyectos .NET y otras decisiones de implementación.
- **No asumir:** que el último commit/documentación ya fue publicado en remoto o que el PR final se creó/mezcló. El último estado conocido indica que aún faltaba hacer push y abrir el PR final.

## 2. Objetivo del producto

Crear una plataforma empresarial para gestionar de forma coherente la identidad operativa, acreditación, credenciales, acceso físico, asistencia/jornada y consumos de comedor del personal propio y de terceros.

La plataforma debe permitir que diferentes clientes/organizaciones contraten solo los módulos que necesitan, manteniendo una base de producto común y una arquitectura extensible.

### Contexto operativo

- Entornos empresariales, industriales y potencialmente mineros.
- Personal propio y personal contratista.
- Posibles múltiples empresas, contratistas, instalaciones, áreas y puntos de control.
- Integración con terminales y proveedores de hardware heterogéneos.
- Credenciales que pueden ser utilizadas en varios procesos: acceso, asistencia, comedor e identificación.
- Configuración de reglas por cliente, instalación, empresa o área, según se defina en el modelo de negocio.

No se ha definido todavía una lista completa de requisitos regulatorios, SLA, volúmenes, concurrencia, disponibilidad, retención de datos ni jurisdicciones aplicables. No inventarlos; levantarlos cuando se diseñe el sistema.
