# D-FE-01 — Cambio de frontend: de React a Blazor Server

> **Estado:** declarada por el usuario; el 2026-10-30 se fijó que **toda la solución pasa a Blazor Server, sin convivencia permanente con React**. Consecuencias técnicas y preguntas abiertas pendientes de validar
> **Origen:** instrucción del usuario durante la sesión del módulo de comedores
> **Reemplaza (solo en tecnología):** la propuesta de frontend de [`ewop/06-frontend-propuesto.md`](../ewop/06-frontend-propuesto.md), que asumía una aplicación React organizada por features. Se conservan sus ideas de organización modular, rutas por módulo y navegación filtrada.

---

## Decisión declarada

El frontend del producto **deja de ser React y pasa a ser Blazor Server**.

**Motivo declarado por el usuario:** reducir el riesgo de ingeniería inversa asistida por IA y de copia del sistema. *(Interpreto "REA" como "ingeniería inversa"; si se refería a otra cosa, corregir este texto.)*

## Alcance: toda la solución en Blazor Server (declarado 2026-10-30)

El usuario declara que **toda la solución pasa a Blazor Server** y descarta que React y Blazor convivan de forma permanente. Hoy existe una SPA React con las pantallas de Etapa 1 (inicio de sesión, compañías y relaciones, unidades organizativas, áreas de acceso, permisos, evaluación de acceso, personas con su historial y credenciales, datos maestros, usuarios y roles). **Se rehacen en Blazor Server y la SPA React se retira.**

| Tema | Consecuencia | Estado |
|---|---|---|
| Alcance de trabajo | Crece: además de Comedores hay que reconstruir las pantallas de Etapa 1. Es trabajo de UI sobre una capa de aplicación ya probada, no una reescritura de reglas | Declarado |
| Autenticación | Desaparecen el doble inicio de sesión y los dos modelos: un solo inicio de sesión en la UI Blazor. Cómo se obtiene y renueva el token hacia la API dentro del circuito se decide en la especificación | Por diseñar (Q-26) |
| **Contexto de usuario y alcance** | Con la decisión de consumir la API por HTTP (abajo), la capa de aplicación sigue ejecutándose **dentro de solicitudes HTTP normales**, donde `HttpContext` es fiable: no hace falta reescribir `IUsuarioActualAccessor` ni `IAlcanceCompaniaAccessor` por el circuito. Lo que sí hay que resolver: (1) el host Blazor debe obtener y renovar el token del usuario y adjuntarlo a cada llamada; (2) el alcance hoy viaja en el token y queda fijo 60 minutos, así que **la API debe resolverlo por solicitud desde la BD** (con caché corta si hace falta) para que un cambio se aplique a un circuito abierto | Brecha técnica (B-02), por diseñar |
| Consumo de datos | Los componentes Blazor consumen la **API HTTP** (cliente tipado), nunca la BD ni la capa de aplicación directamente. Existen contratos OpenAPI en `specs/001-…/contracts` que pueden servir de base del cliente; la herramienta de generación no está decidida | Declarado (2026-10-30) |
| API HTTP y pruebas | 72 endpoints, 246 pruebas de contrato y pruebas de integración que también pasan por HTTP. **Se reutilizan o adaptan los que se puedan; el resto se retira a una carpeta fuera de la solución** (ver sección siguiente) | Declarado (2026-10-30) |
| Pruebas E2E | Las pruebas Playwright de la SPA se sustituyen por pruebas de la UI Blazor | Por diseñar |
| Constitución | Pide "interfaz web" y contratos explícitos entre UI y backend: requiere enmienda | Pendiente |
| Orden | Ver [orden-de-ejecucion.md](../integracion/orden-de-ejecucion.md) | Propuesta |

## Destino de los endpoints y las pruebas (decisión declarada 2026-10-30, cierra Q-28)

**Decisión del usuario:** (1) la UI Blazor consume las APIs **por HTTP**; (2) se **reutilizan o adaptan** los endpoints que se puedan; (3) los que no, se **retiran a una carpeta fuera de la solución**.

**Hechos verificados:** los controladores delegan en servicios de la capa de aplicación; las pruebas de contrato y las de integración llaman a `/api/...` con `HttpClient` (`WebApplicationFactory<Program>`, clientes con JWT). No se leyeron las de unidad.

**Consecuencias y puntos que siguen abiertos:**

| Tema | Consecuencia | Estado |
|---|---|---|
| Clasificación | Cada endpoint se clasifica en **reutilizar tal cual**, **adaptar** (tenant, permisos, alcance por solicitud) o **retirar**. Lo mismo para sus pruebas. Se hace con un inventario aprobado por el usuario antes de tocar nada | Por hacer (prompt 2) |
| Pruebas | Las pruebas de un endpoint siguen la suerte del endpoint: se reutilizan, se adaptan con él (por ejemplo el snapshot OpenAPI cambiará al introducir el tenant) o se retiran con él | Por hacer |
| **Exposición de la API** | El motivo de D-FE-01 es dificultar la copia. Una API HTTP **pública** sigue mostrando sus contratos. Alternativa: API accesible solo en red interna, con Blazor Server como única cara pública (más piezas: dos servicios y autenticación entre ellos). **No está decidido** | Pregunta Q-29 |
| Carpeta de retiro | Debe quedar fuera de la solución y del build. Ubicación propuesta: carpeta `archivo/` en la raíz del repositorio, sin referencias desde el `.slnx` y excluida de Docker. Antes de mover, una etiqueta de Git (por ejemplo `etapa1-api-react-final`) | Pregunta Q-30 |
| Terminales | Los endpoints de ingestión de terminales serán nuevos y de su propio contrato; no dependen de esta decisión | Propuesta |

## Qué aporta y qué no (inferencia técnica)

- **Aporta:** en Blazor Server la lógica de los componentes se ejecuta en el servidor; el navegador recibe solo un script de conexión y actualizaciones de interfaz. No se entrega al cliente el código de la aplicación ni la lógica de la UI, como ocurriría con un paquete JavaScript de React que puede analizarse o desofuscarse.
- **No garantiza:** que el sistema no pueda copiarse. El HTML renderizado, el comportamiento observable, los endpoints expuestos (por ejemplo los de terminales) y la apariencia siguen siendo visibles. Es una **reducción de superficie**, no una protección absoluta. La protección real de datos sigue dependiendo de D8-02 y D8-04.

## Consecuencias para el diseño

| Tema | Consecuencia | Relación con decisiones aprobadas |
|---|---|---|
| Seguridad en backend | Que la UI corra en el servidor **no** sustituye la autorización: cada caso de uso de la capa de aplicación valida tenant, entitlement, permiso y alcance. Ocultar un componente no es un control | D8-04, §4 regla 3 |
| Contexto de tenant | Un circuito de Blazor Server es de larga duración. El tenant, el entitlement y los permisos se **revalidan en cada operación**, no solo al abrir la sesión, para que una gracia vencida o una revocación se apliquen a sesiones ya abiertas | D8-02, D8-04, R8-11, R8-13 |
| Acceso a datos | Los componentes consumen la API HTTP, nunca la BD ni repositorios de otro módulo | D8-05 |
| Conectividad | La UI exige conexión permanente con el servidor. No hay modo offline de la interfaz. El requisito de operar sin conexión (R8-01) aplica a la decisión en terminales, no a la UI | R8-01, D8-01 |
| Despliegue | Cada usuario conectado ocupa memoria y una conexión en tiempo real en el servidor. El dimensionamiento y la afinidad de sesión deben planificarse; en SaaS multi-tenant la carga es compartida | D8-01 |
| Terminales y adaptadores | Siguen entrando por una API o contrato de aplicación propio, independiente de la UI | R8-50, D8-05 |
| Estructura | La organización por módulos de §7 se mantiene: carpetas por módulo con sus componentes, y rutas por módulo. Cambian el framework y los componentes | §7 |
| Exportaciones | El cliente pide reportes en Excel y PDF (módulo de comedores). Deberá elegirse la librería de exportación | módulo comedores |

## Riesgos

- **Latencia:** usuarios alejados de la región del servidor perciben retardo en cada interacción. Impacto a evaluar para sedes remotas.
- **Reconexiones:** una caída breve de red puede reiniciar el estado de la pantalla; hay que diseñar qué se conserva.
- **Escalabilidad de conexiones:** puede requerir un servicio de conexiones administrado; no decidido.
- **Falsa sensación de seguridad:** sin los controles de backend, Blazor Server no protege los datos por sí solo.

## Preguntas abiertas

1. Librería de componentes de UI, de exportación a Excel y PDF, y de pruebas de UI (sin decidir).
2. Estrategia de reconexión y conservación de estado en pantallas largas (cargas masivas).
3. Dimensionamiento, afinidad de sesión y región de despliegue para los clientes del SaaS y de instalaciones dedicadas.
4. Si algún cliente exigirá una interfaz que funcione con conectividad limitada, caso que Blazor Server no cubre.
5. Inventario real de pantallas de la SPA React y de sus pruebas E2E para estimar la migración. El código del frontend no se ha leído.
6. Si se necesitará una API pública para integraciones además de los endpoints de terminales.

## Mantenimiento

Al validar esta decisión, actualizar `docs/README.md` y el apartado de frontend de `CLAUDE.md`, y marcar `ewop/06-frontend-propuesto.md` como reemplazado en tecnología.
