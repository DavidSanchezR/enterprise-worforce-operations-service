# Exportación de contexto técnico — Enterprise Workforce Operations Platform (EWOP)

> **Propósito:** trasladar a Claude el contexto de producto, arquitectura, negocio y estado de trabajo discutido en la conversación, para continuar sin volver a descubrir las decisiones ya planteadas.
>
> **Idioma de trabajo:** español.
>
> **Regla de continuidad:** distinguir explícitamente entre hechos confirmados, recomendaciones/propuestas y decisiones aún pendientes. No asumir que una propuesta fue aprobada ni que una acción Git pendiente ya se ejecutó.

## Índice

1. [Resumen ejecutivo](#1-resumen-ejecutivo)
2. [Objetivo del producto](#2-objetivo-del-producto)
3. [Módulos funcionales](#3-módulos-funcionales)
4. [Conceptos y reglas de dominio importantes](#4-conceptos-y-reglas-de-dominio-importantes)
5. [Principios arquitectónicos acordados como dirección](#5-principios-arquitectónicos-acordados-como-dirección)
6. [Arquitectura de backend propuesta](#6-arquitectura-de-backend-propuesta)
7. [Arquitectura de frontend propuesta](#7-arquitectura-de-frontend-propuesta)
8. [Multi-tenancy, licencias y aislamiento de datos](#8-multi-tenancy-licencias-y-aislamiento-de-datos)
9. [Integración con terminales biométricos](#9-integración-con-terminales-biométricos)
10. [Requisitos de calidad y mantenibilidad](#10-requisitos-de-calidad-y-mantenibilidad)
11. [Estado del repositorio y flujo Git](#11-estado-del-repositorio-y-flujo-git)
12. [Decisiones pendientes](#12-decisiones-pendientes)
13. [Próximos pasos recomendados](#13-próximos-pasos-recomendados)
14. [Instrucciones para Claude](#14-instrucciones-para-claude)

---

## 1. Resumen ejecutivo

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

## 3. Módulos funcionales

Los siguientes cinco módulos son el alcance funcional planteado.

### 3.1. Workforce Accreditation & Compliance
**Nombre en español:** Acreditación Documentaria y Cumplimiento.

Responsabilidad conceptual:
- Administrar y validar documentación de personas y contratistas.
- Determinar si una persona cumple las condiciones documentarias requeridas.
- Permitir incorporación, habilitación, reactivación o cambio de estado operativo cuando se cumplan las reglas aplicables.
- Proveer un estado de elegibilidad que otros módulos puedan consultar.

Principio importante: la acreditación determina una condición de elegibilidad, pero no debe conceder automáticamente todos los permisos de acceso. La elegibilidad puede ser requisito necesario, pero no suficiente para autorizar una acción concreta.

Los tipos documentarios, reglas de vencimiento, flujos de aprobación, excepciones y responsables aún deben especificarse.

### 3.2. Identity & Credential Management
**Nombre en español:** Gestión de Identidades y Credenciales.

Responsabilidad conceptual:
- Gestionar la identidad operativa de las personas dentro de la plataforma.
- Administrar credenciales de identificación.
- Producir o gestionar fotochecks/carnés por empresa, área u otros criterios.
- Considerar tecnologías como QR, RFID y NFC/contactless.
- Vincular de forma controlada personas, identidades, credenciales y los sistemas consumidores.

QR, RFID y NFC no deben modelarse como categorías necesariamente equivalentes o mutuamente excluyentes: RFID es una familia tecnológica y NFC es una tecnología relacionada de corto alcance.

No se ha decidido qué tecnología se utilizará primero ni si la plataforma imprimirá tarjetas directamente o se integrará con software/hardware de impresión.

### 3.3. Physical Access Management
**Nombre en español:** Gestión de Accesos Físicos.

Es el núcleo del proyecto original EAC.

Responsabilidad conceptual:
- Controlar y registrar intentos/eventos de acceso físico.
- Aplicar reglas de autorización a puntos, puertas, áreas o instalaciones, según el alcance que se detalle.
- Tener en cuenta identidad, credencial, estado de acreditación, permisos, horarios y configuración que correspondan.
- Mantener trazabilidad/auditoría de decisiones y eventos.

Los detalles actuales del dominio EAC se encuentran en el repositorio del proyecto; este documento no reemplaza sus especificaciones existentes.

### 3.4. Time & Attendance Management
**Nombre en español:** Gestión de Asistencia y Jornada.

Responsabilidad conceptual:
- Recibir y procesar marcaciones de asistencia desde distintos terminales/proveedores.
- Integrarse con dispositivos de fabricantes como IDEMIA, HID y ZKTeco.
- Mantener eventos de marcación y procesarlos según las reglas de asistencia que se definan.
- Separar la captura técnica de eventos de la interpretación funcional de jornada/asistencia.

Las capacidades, protocolos y comportamientos dependen del modelo y versión del dispositivo. No asumir que todos los equipos de un fabricante ofrecen las mismas interfaces.

### 3.5. Dining & Consumption Management
**Nombre en español:** Gestión de Comedores y Consumos.

Responsabilidad conceptual:
- Gestionar consumos de desayuno, almuerzo, cena, servicio a la carta y bebidas.
- Aplicar ventanas horarias y reglas de servicio.
- Asociar cada consumo a la persona/identidad correspondiente y conservar su trazabilidad.
- Permitir configuración según cliente, instalación o comedor cuando corresponda.

Todavía deben especificarse límites de consumo, subsidios, cuotas, precios, pagos, integración con POS, excepciones, reglas por turno y reportes.

### Capacidades transversales propuestas

Se identificaron como preocupaciones transversales, sujetas a diseño detallado:

- Personas, empresas y contratistas.
- Estructura organizacional y asignaciones a instalaciones/áreas.
- Autorización y control de acceso a funciones.
- Multi-tenancy y aislamiento de información.
- Licenciamiento/entitlements de módulos.
- Auditoría y trazabilidad.
- Configuración por cliente.
- Integraciones externas y gestión de dispositivos.

No convertir automáticamente cada capacidad transversal en un proyecto independiente. Su ubicación debe responder a responsabilidades y dependencias reales.

## 4. Conceptos y reglas de dominio importantes

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

## 5. Principios arquitectónicos acordados como dirección

### 5.1. Monolito modular primero

La recomendación principal fue iniciar con un **monolito modular**:

- Un producto y una solución principal.
- Módulos con límites explícitos.
- Dependencias controladas.
- Contratos claros para comunicación entre módulos.
- Despliegue inicial sencillo en comparación con una arquitectura de microservicios.
- Posibilidad de extraer componentes más adelante si hay razones operativas concretas.

No crear microservicios únicamente porque existan cinco módulos funcionales o porque distintos clientes contraten diferentes módulos.

### 5.2. Monorepo

La recomendación para la etapa actual es mantener el backend y los módulos del producto en un repositorio principal (monorepo), con proyectos separados por responsabilidad cuando sea útil. Esto facilita coherencia de contratos, refactorizaciones coordinadas, pruebas y versionado del producto.

La organización final del frontend (mismo repositorio o subdirectorio claramente delimitado) debe definirse según las herramientas y el stack elegido. El ejemplo propuesto lo coloca en `frontend/`.

### 5.3. Tres conceptos distintos

No hacer coincidir obligatoriamente estas tres dimensiones:

1. **Módulo de negocio:** responsabilidad funcional, por ejemplo asistencia.
2. **Proyecto de código:** unidad de compilación y límite de dependencias, por ejemplo `TimeAttendance.Domain`.
3. **Unidad de despliegue:** aplicación o proceso publicado y ejecutado.

Un módulo de negocio no necesita ser un microservicio ni una aplicación web independiente.

### 5.4. Cohesión alta y acoplamiento bajo

- Cada módulo debe ser responsable de su propio modelo y reglas de negocio.
- Evitar que un módulo escriba directamente en las tablas internas de otro.
- Evitar referencias indiscriminadas entre modelos de dominio.
- Utilizar contratos explícitos, interfaces y/o eventos de aplicación donde se necesite comunicación entre módulos.
- Mantener `SharedKernel` pequeño; no convertirlo en un repositorio de todas las clases compartidas.
- Las abstracciones compartidas deben existir por una razón clara, no solo para evitar duplicación superficial.

### 5.5. Capacidad de evolución

La arquitectura debe permitir separar una capacidad en un servicio independiente cuando haya motivos demostrables, como:
- Necesidad de escalar de manera independiente.
- Requisitos distintos de disponibilidad o mantenimiento.
- Equipos y ciclos de entrega autónomos.
- Aislamiento especial de integraciones.
- Requisitos regulatorios o de seguridad.
- Límites de dominio y datos suficientemente independientes.

La ingestión de eventos de terminales de asistencia se mencionó como posible candidata futura si el volumen, la disponibilidad o el aislamiento de integraciones lo justifican. Esto es una posibilidad, no una decisión de separación ya tomada.

## 6. Arquitectura de backend propuesta

La siguiente estructura es un **boceto conceptual**, no una instrucción de crear todos los proyectos de inmediato:

```text
src/
├── Host/
│   └── Enterprise.WorkforcePlatform.Api/
├── BuildingBlocks/
│   ├── Enterprise.SharedKernel/
│   └── Enterprise.Contracts/
├── Modules/
│   ├── Accreditation/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   ├── IdentityCredentials/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   ├── PhysicalAccess/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   ├── TimeAttendance/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   └── DiningConsumption/
│       ├── Domain/
│       ├── Application/
│       └── Infrastructure/
└── Platform/
    ├── Tenancy/
    ├── Authorization/
    ├── Licensing/
    ├── Audit/
    └── Configuration/
```

### Responsabilidades por capa

- **Domain:** entidades, objetos de valor, reglas e invariantes del dominio. No debe depender de ASP.NET Core, EF Core ni infraestructura.
- **Application:** casos de uso y orquestación; depende del dominio y de abstracciones necesarias.
- **Infrastructure:** persistencia, acceso a sistemas externos, adaptadores y detalles técnicos.
- **API/Host:** punto de entrada y composición del sistema. No debe convertirse en el lugar donde se acumulen las reglas de negocio.
- **Contracts:** contratos de comunicación necesarios entre módulos o integraciones. Deben mantenerse estables y mínimos.
- **SharedKernel:** solo conceptos genuinamente comunes y estables; mantenerlo reducido.
- **Platform:** capacidades técnicas/operativas transversales, con límites claros. Debe evaluarse si cada área merece un proyecto independiente o si algunas son solo componentes dentro del host al principio.

### Dependencias esperadas

Dirección general:
- `Domain` no depende de `Application`, `Infrastructure` ni del framework web.
- `Application` depende de `Domain` y de abstracciones.
- `Infrastructure` implementa abstracciones usadas por `Application`.
- `API/Host` conecta dependencias e invoca casos de uso.
- Un módulo no debe saltarse sus contratos para acceder a las tablas/repositorios internos de otro módulo.

El grafo exacto de referencias de proyectos debe diseñarse y validarse antes de implementar.

## 7. Arquitectura de frontend propuesta

Se recomendó inicialmente una aplicación web modular, organizada por funcionalidades, en vez de una aplicación web independiente por cada módulo:

```text
frontend/src/
├── app/
├── shared/
└── features/
    ├── accreditation/
    ├── identity-credentials/
    ├── physical-access/
    ├── time-attendance/
    └── dining-consumption/
```

- `app/`: composición, navegación y estructura principal de la aplicación.
- `shared/`: componentes y utilidades transversales realmente compartidos.
- `features/`: pantallas, componentes y lógica de presentación por módulo.

### URLs y navegación

Las rutas pueden representar módulos funcionales, por ejemplo:

- `/acreditacion`
- `/identidades-credenciales`
- `/accesos`
- `/asistencia`
- `/comedores`

Son ejemplos conceptuales; no están definidas como rutas definitivas.

La aplicación puede mostrar u ocultar opciones de navegación según:
1. Los módulos contratados por el tenant.
2. Los permisos del usuario.
3. La configuración funcional aplicable.

El backend debe repetir y hacer cumplir los controles. No confiar en el frontend para proteger datos u operaciones.

### ¿Cuándo separar las aplicaciones web?

Usar subdominios, frontends o aplicaciones distintas solo si aparecen razones reales, como:
- Equipos diferentes con autonomía de entrega.
- Ciclos de despliegue incompatibles.
- Necesidades distintas de seguridad o aislamiento.
- Experiencias de usuario radicalmente diferentes.
- Requisitos de disponibilidad o escalabilidad independientes.

La contratación separada de módulos, por sí sola, no obliga a separar los frontends.

## 8. Multi-tenancy, licencias y aislamiento de datos

Este punto permanece **pendiente de decisión formal**.

### 8.1. Separar tres mecanismos

| Mecanismo | Pregunta que responde |
|---|---|
| Entitlement/licencia | ¿Qué módulos o capacidades tiene contratados el cliente? |
| Autorización | ¿Qué acciones puede realizar este usuario en este contexto? |
| Configuración funcional | ¿Qué reglas, horarios, servicios, parámetros o modalidades están habilitados? |

El backend debe validar de forma consistente los tres aspectos cuando corresponda. Por ejemplo, un usuario no debe poder invocar una API del módulo de comedor si el tenant no lo tiene habilitado, aunque el usuario conozca la URL.

### 8.2. Entidades organizativas que deben distinguirse

El diseño debe analizar explícitamente:
- Tenant/cliente de la plataforma.
- Empresa/mandante/principal.
- Contratista.
- Persona.
- Instalación/sede.
- Área o recurso operativo.
- Usuario de la plataforma.
- Suscripción/contrato y entitlements.

No asumir que `tenant`, `company`, `Principal/Mandante`, `contractor`, `site` y `subscription` son sinónimos.

Un tenant puede necesitar varias sedes y relacionarse con empresas contratistas; las relaciones concretas aún deben definirse.

### 8.3. Alternativas de aislamiento de datos pendientes

No se seleccionó todavía una alternativa definitiva:

1. **Base de datos compartida con aislamiento lógico estricto por tenant.**
2. **Base de datos separada por tenant.**
3. **Infraestructura y despliegue completamente separados por cliente.**

Estas opciones pueden tener implicaciones distintas en coste, operación, actualizaciones, seguridad, recuperación, reporting y personalización. La elección debe basarse en requisitos reales de clientes, volumen, riesgo, regulación y capacidad operativa.

### 8.4. Modelos de despliegue pendientes

Tampoco se seleccionó todavía uno de estos modelos:

1. **SaaS centralizado:** plataforma operada de manera centralizada para múltiples clientes.
2. **Instalación dedicada por cliente:** cada cliente tiene su despliegue independiente.
3. **Híbrido (SaaS + on-premises):** combinación de servicios centralizados y componentes en las instalaciones del cliente.

La selección del despliegue y la selección del aislamiento de datos están relacionadas, pero no son exactamente la misma decisión.

## 9. Integración con terminales biométricos

La recomendación es crear una capa de contratos de integración y adaptadores específicos por proveedor/modelo, sin propagar las peculiaridades de cada fabricante al dominio de asistencia.

```text
Time Attendance
       |
       v
Device Integration Contracts
       |
       +--> IDEMIA Adapter
       +--> HID Adapter
       +--> ZKTeco Adapter
       +--> Other Vendor/Model Adapters
```

Este esquema es conceptual; no implica que todos los adaptadores ya existan ni que todos los dispositivos sean compatibles.

### Principios

- El dominio de asistencia debe consumir conceptos normalizados, no detalles de protocolo del fabricante.
- El adaptador gestiona particularidades técnicas del dispositivo y transforma eventos a contratos internos.
- Las diferencias por modelo, versión, firmware, protocolo y capacidades deben validarse.
- Definir idempotencia, deduplicación, timestamps, zona horaria, secuencias, eventos tardíos, reintentos y observabilidad al especificar la ingestión.
- Diseñar el manejo de desconexiones y sincronización si los requisitos de operación lo exigen.
- Evitar asumir que es necesario centralizar plantillas biométricas. Evaluar si el dispositivo solo entrega un identificador/resultado o si se necesita tratar datos biométricos más sensibles.
- Revisar privacidad, seguridad, acceso, retención y normativa aplicable antes de cerrar la arquitectura de biometría.

Los detalles técnicos y funcionales de cada fabricante todavía necesitan investigación y especificación; no deben inventarse protocolos ni capacidades.

## 10. Requisitos de calidad y mantenibilidad

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

## 11. Estado del repositorio y flujo Git

### Información conocida

- Nombre del repositorio: `enterprise-access-control`.
- GitHub: `DavidSanchezR/enterprise-access-control`.
- Producto original: Enterprise Access Control (EAC).
- Existe una historia de baseline, validación post-baseline y documentación/presentación final.
- Según el último estado compartido, el baseline estaba cerrado y fusionado.
- El resumen anterior reportó la validación VF-001–VF-011 cerrada y documentación final preparada.

### Estado Git conocido más reciente

Se creó localmente el commit:

```text
a69ab3b docs: finalize project documentation and presentation
```

En la rama local:

```text
docs/final-delivery
```

La rama `main` local y `origin/main` estaban en:

```text
dc1edea Merge PR #6 from docs/close-post-baseline-validation
```

La rama `docs/final-delivery` tenía un commit adicional con respecto a `main`. El comando `git branch -vv` indicaba que esa rama todavía no tenía upstream configurado.

**Importante:** el último estado conocido no confirma que se haya ejecutado `git push`, ni que se haya abierto o fusionado el PR final. No afirmar que el trabajo ya está en GitHub hasta verificarlo.

### Próxima secuencia Git que se había recomendado

Primero revisar qué contiene el commit final respecto a `main`:

```bash
git diff main..HEAD --stat
```

Propósito: mostrar un resumen de los archivos añadidos, eliminados o modificados entre la rama actual (`HEAD`) y `main`, sin cambiar archivos ni crear commits. Ejecutarlo desde `docs/final-delivery`.

Si la lista es correcta, publicar la rama y configurar el upstream:

```bash
git push -u origin docs/final-delivery
```

Propósito:
- `push`: publicar la rama local en el remoto.
- `-u` (`--set-upstream`): vincular la rama local con la rama remota para facilitar futuros `git push`/`git pull`.
- `origin`: nombre habitual del remoto.
- `docs/final-delivery`: nombre exacto de la rama.

Luego abrir un PR de `docs/final-delivery` hacia `main`, revisar el diff y fusionarlo según el flujo del repositorio. Antes de ejecutar acciones, verificar el estado actual porque puede haber cambiado desde el último resumen.

### Contenido de entrega mencionado

El trabajo de documentación final incluía, según el contexto compartido:
- Manuales de despliegue, usuario y administración.
- Documentación de cierre técnico.
- Documentos de trazabilidad.
- Diagramas de arquitectura/despliegue.
- Presentación final `EAC-Spec-Kit-David-Sanchez-Rafael.pptx`.
- Referencia al cierre de baseline T001–T242.
- Validaciones VF-001–VF-011.
- Validación manual de UI.
- PR post-baseline #5 fusionado, según el estado reportado anteriormente.

No asumir que todos esos archivos se encuentran en el commit remoto hasta revisar el diff/PR. El resumen es contexto; el repositorio es la fuente de verdad para los archivos y el estado real.

### Continuidad con SpecKit y Claude

El proyecto EAC fue trabajado con SpecKit y Claude, incluyendo checkpoints/tareas. No hay aquí un estado suficientemente detallado para reconstruir el contenido exacto de cada tarea, por lo que se debe consultar los artefactos de SpecKit, especificaciones, historial y documentación del repositorio antes de reanudar tareas técnicas. No inventar identificadores ni marcar tareas como completadas sin evidencia.

## 12. Decisiones pendientes

Estas cuestiones deben resolverse antes de cerrar la arquitectura de referencia:

### Prioridad alta

1. **Modelo de despliegue:** SaaS centralizado, dedicado por cliente o híbrido.
2. **Aislamiento de datos:** base compartida con aislamiento lógico, base por tenant o infraestructura dedicada.
3. **Modelo organizativo:** definición formal de tenant, mandante/principal, contratista, instalación/sede, área y sus relaciones.
4. **Stack de implementación:** confirmar versiones de .NET, framework frontend, persistencia, autenticación y despliegue.
5. **Límites de módulos/proyectos:** validar cuáles son proyectos de código reales y cuáles deben permanecer como componentes internos.
6. **Estrategia de identidad/autorización:** modelo de usuarios, roles, permisos y contexto de tenant.

### Prioridad funcional

7. Flujos y reglas de acreditación, vencimientos, aprobaciones, suspensión y reactivación.
8. Modelo de credenciales: tarjeta, QR, RFID/NFC, identificadores externos, emisión y revocación.
9. Modelo de autorización física: recursos, puertas, áreas, horarios, reglas y eventos.
10. Reglas de asistencia: marcaciones, turnos, excepciones, conciliación y reportes.
11. Reglas de comedor: servicios, ventanas horarias, consumos permitidos, precios/subsidios y excepciones.
12. Requisitos de integración por fabricante/modelo de terminal.
13. Política de privacidad, seguridad y retención para datos de identidad y biometría.
14. Requisitos no funcionales: disponibilidad, rendimiento, volumen de eventos, recuperación, observabilidad y auditoría.
15. Modelo comercial y de licenciamiento: módulos, capacidades, límites y cambios de suscripción.

### Prioridad de operación

16. Estrategia de actualización y migración de esquema.
17. Observabilidad, logs, métricas, trazas y alertas.
18. Estrategia de backups/restore y recuperación ante desastres.
19. CI/CD, entornos, secretos y configuración por tenant.
20. Criterios que justificarían extraer un módulo a un servicio independiente.

## 13. Próximos pasos recomendados

Orden sugerido para continuar sin generar código prematuramente:

### Paso 1 — Verificar el estado real del repositorio

- Revisar la rama actual, estado del working tree, ramas remotas y PRs.
- Confirmar si `docs/final-delivery` fue publicada y si el PR final se abrió/fusionó.
- Inspeccionar los archivos de especificación y documentación de EAC.
- Preservar el historial y las decisiones que ya están en el repo.

### Paso 2 — Confirmar el producto y el nombre

- Decidir si EWOP será el nombre de trabajo.
- Mantener EAC como módulo/proyecto original si tiene sentido, sin renombrar todo automáticamente.
- Antes de un uso comercial, comprobar disponibilidad de marca y dominio; no se ha verificado todavía.

### Paso 3 — Resolver modelo de despliegue y aislamiento

Elegir explícitamente:
- SaaS centralizado, dedicado o híbrido.
- Aislamiento lógico, base por tenant o infraestructura dedicada.

Documentar razones, consecuencias y requisitos que motivan la elección.

### Paso 4 — Definir el modelo organizativo

Modelar relaciones entre tenant, empresas, mandante/principal, contratistas, personas, instalaciones, áreas, usuarios y suscripciones. No empezar por crear tablas: primero fijar definiciones y reglas.

### Paso 5 — Delimitar módulos y contratos

Para cada módulo:
- Responsabilidad.
- Entidades/objetos bajo su propiedad.
- Casos de uso.
- Datos que expone a otros módulos.
- Eventos que publica/consume.
- Dependencias permitidas.
- Límites de transacción.
- Pruebas de aceptación.

### Paso 6 — Diseñar la solución de referencia

- Confirmar el stack.
- Diseñar proyectos .NET y referencias.
- Diseñar host/API y registro de módulos.
- Definir autenticación, autorización, tenant context y entitlements.
- Diseñar persistencia y migraciones.
- Definir integración de dispositivos mediante contratos y adaptadores.
- Diseñar frontend modular y rutas.
- Definir pruebas unitarias, integración, contrato, seguridad y aislamiento multi-tenant.

### Paso 7 — Crear una base vertical mínima

Implementar solo después de aprobar decisiones anteriores:
- Arranque de host/API.
- Autenticación y contexto de tenant según la estrategia elegida.
- Un módulo vertical representativo.
- Un contrato de módulo.
- Persistencia y pruebas.
- Verificación de autorización y entitlement en backend.
- CI básica.

Evitar crear todos los proyectos vacíos sin una estructura de dependencias validada.

### Paso 8 — Planificar la evolución por incrementos

Orden funcional sugerido para discutir, no aprobado:
1. Consolidar el módulo de acceso físico existente.
2. Establecer identidad operativa y credenciales como base compartida.
3. Añadir acreditación/compliance y su relación con elegibilidad.
4. Incorporar asistencia con adaptadores de terminales.
5. Incorporar consumos de comedor.
6. Añadir licenciamiento por módulo, configuración y reportes de forma transversal según los requisitos.

El orden final debe basarse en prioridades de negocio, dependencias reales y estado del código EAC existente.

## 14. Instrucciones para Claude

Al recibir este documento:

1. **No reiniciar el análisis desde cero.** Utilizarlo como contexto inicial y luego verificar las afirmaciones relevantes contra el repositorio.
2. **No asumir que las propuestas son decisiones aprobadas.** Especialmente el nombre EWOP, el monolito modular, la estructura de carpetas, el despliegue y el aislamiento de datos.
3. **Primero verificar Git.** Confirmar estado actual de `docs/final-delivery`, `main`, `origin/main` y PR final. El último estado conocido es histórico y puede haber cambiado.
4. **Inspeccionar los artefactos SpecKit existentes** antes de crear especificaciones nuevas o alterar identificadores de tareas.
5. **Conservar los términos de dominio** y distinguir persona, identidad, credencial, identificador tecnológico, acreditación, autorización y evento.
6. **No proponer microservicios por defecto.** Partir del monolito modular salvo evidencia de una necesidad independiente de escalado, despliegue, seguridad, disponibilidad o propiedad.
7. **No generar estructura/código masivo antes de resolver** modelo de despliegue, aislamiento de datos, tenant y límites de módulos.
8. **Explicar comandos Git nuevos** antes de pedir al usuario que los ejecute: indicar propósito, efecto y si modifican archivos, historial o remoto.
9. **No afirmar que una acción se completó** sin salida de herramienta, evidencia de repositorio o confirmación explícita del usuario.
10. Al proponer cambios arquitectónicos, separar:
    - hechos observados en el repositorio;
    - decisiones confirmadas por el usuario;
    - recomendaciones técnicas;
    - preguntas que requieren decisión del usuario.
11. Priorizar una arquitectura mantenible, con bajo acoplamiento, alta cohesión, seguridad multi-tenant y contratos de integración claros.
12. Mantener el español como idioma principal, con nombres técnicos en inglés cuando sean estándar.

### Primer mensaje sugerido para continuar con Claude

> Lee este documento como contexto de continuidad del proyecto. Antes de modificar código, verifica el estado actual del repositorio `DavidSanchezR/enterprise-access-control`, confirma si el PR de `docs/final-delivery` fue publicado/abierto/fusionado y revisa los artefactos SpecKit existentes. Después, ayúdame a cerrar las decisiones pendientes de despliegue y aislamiento de datos, una por una. No implementes todavía una estructura completa de proyectos hasta que validemos el modelo de tenant y los límites de módulos.
