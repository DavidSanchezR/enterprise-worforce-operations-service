# Comedores — Reportes y exportaciones

> **FUERA DE ALCANCE en esta etapa** (declarado por el usuario, 2026-10-30, [D-ALC-01](../../decisiones/D-ALC-01-alcance-etapa-comedores.md)): no se construye ningún reporte ni exportación. Este archivo se conserva como registro de lo que pidió el cliente, para una etapa posterior.

> **Consultar cuando:** se construyen reportes, consultas o exportaciones del módulo.
> **Certeza:** el contenido proviene de la sección 4.9 a 4.16 del documento fuente y de los comentarios decodificados del cliente. La estructura de columnas se describe por comportamiento; no se copia el diseño de los reportes originales.

## Estado por reporte

| ID | Reporte | Decisión del documento | Pedido del cliente | Formato pedido | Estado |
|---|---|---|---|---|---|
| REP-COM-01 | Consumo agrupado por compañía (consulta) | Se cubre con el reporte de consumos que detalla datos de compañías | Conforme | No indicado | Confirmado |
| REP-COM-02 | Consumo por persona (consulta) | Misma funcionalidad | Conforme | No indicado | Confirmado |
| REP-COM-03 | Acceso a comedor (fecha de asignación) | No se considera | Se puede requerir, como **resumen consolidado por centro de costos por cada servicio de alimentos** | No indicado | Por confirmar |
| REP-COM-04 | Consumo de comedores (por compañía contratista) | No se considera | Considerar como reporte en **Excel, no PDF** | Excel | Por confirmar |
| REP-COM-05 | Consolidado de consumos (cantidad y costo por compañía) | No se considera | Considerar como reporte en **Excel, no PDF** | Excel | Por confirmar |
| REP-COM-06 | Detalle de consumo (diario, por servicio) | Reportes similares con estructura distinta | Debe incluir **categoría del trabajador** (empleado, obrero, practicante) y **cargo**; agregar columnas **DNI, Área y código personal** | Excel (el original exportaba a Excel) | Confirmado, con columnas nuevas |
| REP-COM-07 | Resumen mensual de consumo (por día y por empresa, con estadística) | No existe como tal; el área debe definir si lo usa | Queda como reporte | Excel | Por confirmar uso |
| REP-COM-08 | Consumos negados (con motivo) | Se tiene un reporte equivalente | Queda como reporte | Excel y PDF, según lo requiera el usuario | Confirmado |

**Observaciones**
- En REP-COM-03, 04 y 05 el documento dice "no se considera" y el cliente pide una versión; la contradicción está registrada en [08-preguntas-abiertas.md](08-preguntas-abiertas.md).
- El texto del comentario de REP-COM-06 aparece duplicado en el PDF. Se interpreta como un solo pedido de columnas. Falta confirmar si aplica también a REP-COM-07.
- El sentido de "costo" en el consolidado (cantidad y monto por compañía) sugiere uso para control o cobro, pero el documento no lo explica.

## Requisitos transversales de reportes

| ID | Requisito | Origen |
|---|---|---|
| RT-REP-01 | Todo reporte respeta tenant, permisos y alcance (mandante, sede, contratista) del usuario | [P8] D8-04 |
| RT-REP-02 | Exportar es una operación de **consulta o exportación de histórico**: permitida aunque el módulo esté restringido (dentro del plazo de conservación), con permiso propio y auditada | [P8] R8-21, D8-04 |
| RT-REP-03 | Un usuario de contratista solo ve datos de su propia empresa | [P8] R8-14 |
| RT-REP-04 | Los reportes usan el contexto guardado en cada consumo (R8-48), no el vínculo actual de la persona | [P8] R8-16, R8-48 |
| RT-REP-05 | Los datos de personas bloqueadas (R8-36) no se muestran ni se exportan identificados | [P8] R8-36 |
| RT-REP-06 | Los consumos en cuarentena (R8-12) no aparecen en reportes mientras estén en cuarentena | [P8] R8-12 |
| RT-REP-07 | No se incluyen datos biométricos en ningún reporte | [P8] R8-52 |

## Propuesta de prioridad (inferida, a validar)

1. Detalle de consumo (REP-COM-06) y consumos negados (REP-COM-08): confirmados por el cliente y los más ligados a la operación.
2. Consultas por persona y por compañía (REP-COM-01 y 02).
3. REP-COM-03, 04, 05 y 07 solo después de aclarar su alcance.

## Pendiente de librería

Exportación a Excel y PDF: sin decidir (ver [D-FE-01](../../decisiones/D-FE-01-frontend-blazor-server.md)).
