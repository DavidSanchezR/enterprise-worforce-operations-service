# Documentación de contexto — Enterprise Workforce Operations Platform (EWOP)

> Contexto técnico y decisiones de arquitectura del producto, dividido en archivos pequeños para consultarlos **solo cuando corresponda**. Idioma de trabajo: español.

## Regla de uso (para ahorrar contexto)

1. **No cargues toda la carpeta.** Lee este índice y abre únicamente el archivo de la columna "Consultar cuando".
2. Para cualquier tema de tenants, licencias, aislamiento, roles u organización, empieza por [`multitenancy/README.md`](multitenancy/README.md).
3. Distingue siempre entre **declarado** (dicho por el usuario), **inferido**, **recomendación** y **pendiente**. Una propuesta no es una decisión aprobada.
4. El repositorio es la fuente de verdad del estado real; estos documentos son contexto y pueden estar desactualizados.

## Estado de las decisiones (resumen)

| ID | Tema | Estado |
|---|---|---|
| D8-01 | Modelo de despliegue (agnóstico: SaaS o dedicado, más componente local en sede) | Aprobada |
| D8-02 | Aislamiento de datos (lógico estricto como base; ubicación portable del tenant) | Aprobada |
| D8-03 | Modelo organizativo (entidades con relaciones; tenant = grupo empresarial) | Aprobada |
| D8-04 | Entitlement, autorización y configuración funcional | Aprobada |
| D8-05 | Implicaciones para el monolito modular | Aprobada |

| D-FE-01 | Frontend: toda la solución en Blazor Server; la SPA React se retira y sus pantallas se reconstruyen (reduce, no elimina, el riesgo de copia) | Declarada, ver [decisiones/D-FE-01-frontend-blazor-server.md](decisiones/D-FE-01-frontend-blazor-server.md) |
| D-ALC-01 | Alcance de la etapa: solo Comedores, con tenant y licenciamiento completos; sin reportes, horario de interfaces, migraciones ni consumos manuales | Declarada, ver [decisiones/D-ALC-01-alcance-etapa-comedores.md](decisiones/D-ALC-01-alcance-etapa-comedores.md) |

El **stack está verificado** en el repositorio (.NET 10, EF Core 10, SQL Server, JWT; ver `integracion/`). Siguen **sin decidir**: frontera de Comedores y ubicación de Platform, esquema de autenticación de la UI Blazor y destino de los endpoints HTTP, reglas funcionales de cada módulo y las preguntas de [`multitenancy/riesgos-y-preguntas-abiertas.md`](multitenancy/riesgos-y-preguntas-abiertas.md).

## Contexto técnico original (`ewop/`)

| Archivo | Consultar cuando | Líneas | Tokens aprox. |
|---|---|---|---|
| [ewop/01-resumen-y-objetivo.md](ewop/01-resumen-y-objetivo.md) | Al empezar un trabajo nuevo o cuando haga falta recordar qué es EWOP y qué está confirmado frente a propuesto | 40 | ~870 |
| [ewop/02-modulos-funcionales.md](ewop/02-modulos-funcionales.md) | Al trabajar en un módulo concreto o al definir qué responsabilidad corresponde a cada uno | 87 | ~1,141 |
| [ewop/03-dominio-y-reglas.md](ewop/03-dominio-y-reglas.md) | Siempre que se modele una entidad o regla: evita mezclar persona, identidad, credencial, acreditación, autorización y eventos | 38 | ~823 |
| [ewop/04-principios-arquitectonicos.md](ewop/04-principios-arquitectonicos.md) | Al diseñar límites de módulos, dependencias o decidir si algo debe extraerse como servicio | 58 | ~788 |
| [ewop/05-backend-propuesto.md](ewop/05-backend-propuesto.md) | Al diseñar proyectos .NET, capas y referencias (boceto conceptual, no estructura definitiva) | 67 | ~661 |
| [ewop/06-frontend-propuesto.md](ewop/06-frontend-propuesto.md) | Al diseñar el frontend, rutas o navegación por módulos contratados | 56 | ~470 |
| [ewop/07-terminales-biometricos.md](ewop/07-terminales-biometricos.md) | Al trabajar con adaptadores de IDEMIA, HID, ZKTeco, ingestión de eventos o biometría | 36 | ~515 |
| [ewop/08-calidad-y-mantenibilidad.md](ewop/08-calidad-y-mantenibilidad.md) | Al revisar diseño o código contra las reglas prácticas del proyecto | 34 | ~449 |
| [ewop/09-estado-git-historico.md](ewop/09-estado-git-historico.md) | Solo al retomar trabajo de Git o documentación final de EAC; verificar siempre el estado real antes de actuar | 84 | ~910 |
| [ewop/10-decisiones-pendientes-y-proximos-pasos.md](ewop/10-decisiones-pendientes-y-proximos-pasos.md) | Al planificar la siguiente sesión o decidir qué cerrar antes de implementar | 116 | ~1,370 |
| [ewop/11-instrucciones-para-claude.md](ewop/11-instrucciones-para-claude.md) | Al iniciar una sesión de trabajo con Claude sobre EWOP | 31 | ~686 |

## Punto 8: multi-tenancy, licencias y aislamiento (`multitenancy/`)

| Archivo | Consultar cuando | Líneas | Tokens aprox. |
|---|---|---|---|
| [multitenancy/README.md](multitenancy/README.md) | Siempre primero: resumen, decisiones y terminología | 48 | ~1,089 |
| [multitenancy/requisitos-declarados-R8.md](multitenancy/requisitos-declarados-R8.md) | Cuando haga falta saber qué dijo exactamente el usuario o de qué requisito sale una decisión | 66 | ~1,752 |
| [multitenancy/D8-01-despliegue.md](multitenancy/D8-01-despliegue.md) | Al decidir dónde y cómo se despliega el producto (SaaS, dedicado, componente local en sede) | 18 | ~543 |
| [multitenancy/D8-02-aislamiento-datos.md](multitenancy/D8-02-aislamiento-datos.md) | Al diseñar persistencia, acceso a datos, migraciones, backups o pruebas de aislamiento entre tenants | 34 | ~913 |
| [multitenancy/D8-03-modelo-organizativo.md](multitenancy/D8-03-modelo-organizativo.md) | Al modelar tenant, mandante, contratista, sede, área, persona, vínculo, identidad operativa o usuario | 33 | ~833 |
| [multitenancy/D8-04-licencias-autorizacion-configuracion.md](multitenancy/D8-04-licencias-autorizacion-configuracion.md) | Al implementar licencias, límites, gracia, retención, roles, alcance, configuración o acceso del soporte | 37 | ~1,445 |
| [multitenancy/D8-05-monolito-modular.md](multitenancy/D8-05-monolito-modular.md) | Al diseñar módulos, contratos entre módulos, transacciones, auditoría, propiedad de datos o pruebas | 33 | ~1,350 |
| [multitenancy/marco-legal-peru.md](multitenancy/marco-legal-peru.md) | Antes de fijar plazos de conservación, políticas de eliminación, bloqueo o tratamiento de biometría | 18 | ~563 |
| [multitenancy/riesgos-y-preguntas-abiertas.md](multitenancy/riesgos-y-preguntas-abiertas.md) | Al planificar qué validar o decidir a continuación, o al revisar riesgos antes de implementar | 28 | ~595 |
| [multitenancy/impacto-en-otras-secciones.md](multitenancy/impacto-en-otras-secciones.md) | Al actualizar §6, §9 o §12 del contexto original, o al comprobar qué quedó decidido | 13 | ~268 |

## Integración con la solución existente (`integracion/`)

| Archivo | Consultar cuando |
|---|---|
| [integracion/informe-brechas-eac-vs-comedores.md](integracion/informe-brechas-eac-vs-comedores.md) | Antes de diseñar o tocar código: qué existe en EAC, qué falta frente al punto 8 y qué decisiones siguen abiertas |
| [integracion/linea-base.md](integracion/linea-base.md) | Estado real verificado del repositorio: compilación, pruebas y pantallas. Leer antes de retomar el proyecto |
| [integracion/orden-de-ejecucion.md](integracion/orden-de-ejecucion.md) | Siempre que se pregunte qué sigue: fases, puntos de control, decisiones y cuándo abrir VS Code |
| [integracion/prompts-de-arranque.md](integracion/prompts-de-arranque.md) | Al empezar a trabajar con Claude Code: secuencia de prompts con entradas, restricciones y criterio de terminado |
| [integracion/prompts-migracion-pantallas.md](integracion/prompts-migracion-pantallas.md) | Al migrar pantallas de React a Blazor: piloto, prompt repetible por grupo, verificación y retiro |
| [integracion/paridad-pantallas.md](integracion/paridad-pantallas.md) | Estado de cada pantalla migrada; condición para retirar React |
| [origen/](origen/) | Solo para auditoría: el contexto técnico original y la sección del punto 8 tal como se produjeron. Los archivos de `ewop/` y `multitenancy/` son su versión dividida y vigente |

## Módulo Comedores (`modulos/comedores/`)

Análisis del documento funcional del módulo (cliente CMC). Empieza por [modulos/comedores/README.md](modulos/comedores/README.md); luego abre solo el archivo que necesites: `01-fuente-y-alcance`, `02-requisitos-funcionales`, `03-modelo-de-dominio`, `04-reglas-de-negocio`, `05-reportes`, `06-integracion-con-plataforma`, `07-backlog-y-dependencias`, `08-preguntas-abiertas`. El documento fuente es confidencial de un tercero: no copiar sus pantallas ni nombres.

## Mapa de secciones (§) del contexto original

Los documentos conservan referencias del tipo "§4" o "§5.4". Equivalencias:

| § original | Archivo actual |
|---|---|
| §1, §2 | `ewop/01-resumen-y-objetivo.md` |
| §3 | `ewop/02-modulos-funcionales.md` |
| §4 | `ewop/03-dominio-y-reglas.md` |
| §5 | `ewop/04-principios-arquitectonicos.md` |
| §6 | `ewop/05-backend-propuesto.md` |
| §7 | `ewop/06-frontend-propuesto.md` (tecnología **reemplazada** por D-FE-01) |
| §8 (original, pendiente) | **Reemplazada** por `multitenancy/` |
| §9 | `ewop/07-terminales-biometricos.md` |
| §10 | `ewop/08-calidad-y-mantenibilidad.md` |
| §11 | `ewop/09-estado-git-historico.md` |
| §12, §13 | `ewop/10-decisiones-pendientes-y-proximos-pasos.md` |
| §14 | `ewop/11-instrucciones-para-claude.md` |
| §8.1 a §8.11 (nuevos) | `multitenancy/` (ver tabla de archivos) |

## Mantenimiento

- Al cerrar una pregunta abierta o tomar una decisión nueva, actualiza solo el archivo afectado y `multitenancy/riesgos-y-preguntas-abiertas.md`.
- Mantén cada archivo por debajo de unas 150 líneas cuando sea posible; si crece, divídelo.
