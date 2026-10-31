# Comedores — Reglas de negocio

> **Consultar cuando:** se implementan validaciones, restricciones o el flujo de consumo.
> **Certeza:** marcadas por origen: **[Fuente]** dicho por el documento, **[Cliente]** comentario del cliente, **[P8]** decisión o requisito del punto 8, **[Inferido]** deducción de quien redactó, **[Pendiente]** sin definir.

## Habilitación y acceso

| ID | Regla | Origen |
|---|---|---|
| RN-COM-01 | Una empresa contratista debe estar registrada y habilitada para usar el comedor antes de que su personal consuma | [Fuente] 3.1 |
| RN-COM-02 | La habilitación está condicionada a una relación contractual vigente respaldada por contrato, orden de servicio u otro documento vinculante | [Fuente] 3.1 |
| RN-COM-03 | El acceso se asigna por persona (individual o masivo) a un servicio de un comedor, con vigencia por fechas, días de la semana y horas | [Fuente] 3.2, 4.4 |
| RN-COM-04 | Una restricción (definitiva o por rango de fechas) impide el consumo aunque exista acceso asignado | [Fuente] 4.6, [Inferido] prioridad |
| RN-COM-05 | El acceso a un servicio vence al terminar su rango de fechas | [Inferido] |
| RN-COM-06 | La acreditación documentaria es condición necesaria pero no suficiente para consumir: el acceso al servicio se autoriza aparte | [P8/§4] regla 1 |

## Servicio y horario

| ID | Regla | Origen |
|---|---|---|
| RN-COM-07 | El servicio de un comedor tiene horario de inicio y fin; el tótem emite el ticket según el horario y el permiso de la persona | [Fuente] 2.1 |
| RN-COM-08 | Cada servicio de un comedor tiene un consumo máximo | [Fuente] 4.2; su semántica (por día, por turno, por comedor) está **[Pendiente]** |
| RN-COM-09 | Cada servicio de un comedor tiene indicadores de "servicio especial", "servicio excluyente" y "validar sin conexión" | [Fuente] 4.2; su significado está **[Pendiente]** |
| RN-COM-10 | Los horarios de atención de CMC ya están establecidos; la configuración de "horario de interfaces" no se usa y **queda fuera de alcance** | [Cliente] 4.3, [Usuario] |

## Consumo

| ID | Regla | Origen |
|---|---|---|
| RN-COM-11 | Todo consumo y toda marcación se registra, incluso la denegada, con su motivo | [P8] R8-53, [Fuente] 4.16 |
| RN-COM-12 | El consumo guarda el contexto del momento (comedor, servicio, mandante, contratista, identidad operativa, centro de costo, precio y moneda) y no se reescribe | [P8] R8-48, [Inferido] precio |
| RN-COM-13 | La validación offline del tótem solo cubre reglas simples (lista y horario). Las restricciones por rango de horas y por comedor no se validan sin conexión y el consumo queda como **observado**, con notificación de exceso y posible sanción o costo adicional según regla | [P8] R8-51 |
| RN-COM-14 | Con la licencia restringida tras la gracia, el comedor sigue operando con las reglas cargadas y los consumos nuevos van a cuarentena, sin procesar | [P8] R8-12, R8-22 |
| RN-COM-15 | **(Fuera de alcance en esta etapa)** Un consumo manual es una contingencia por consumos no registrados (por ejemplo caída de energía) y debe quedar identificado como manual y con quién lo cargó | [Fuente] 4.7, [Inferido] trazabilidad |
| RN-COM-16 | Los consumos denegados incluyen, al menos, persona o credencial inexistente y sin servicio asignado | [Fuente] 4.16 (ejemplos), lista completa **[Pendiente]** |

## Tensión a resolver

La fuente configura "validar sin conexión" **por servicio de cada comedor** y el punto 8 (R8-51) dice que en comedor no se puede validar offline el rango de horas ni el comedor. Puede significar que el equipo valida offline solo la lista de permisos y que lo demás se evalúa después, o que la fuente permitía validar más. Es una **pregunta abierta** (ver [08-preguntas-abiertas.md](08-preguntas-abiertas.md)); hasta aclararla no debe implementarse la lógica offline del comedor.

## Operaciones sensibles a auditar (propuesta)

Cambios de precio u horario de un servicio, habilitación y deshabilitación de contratistas, asignaciones masivas, restricciones, y desactivación de servicios o comedores. Siguen la regla de auditoría atómica de D8-05 (R8-49).
