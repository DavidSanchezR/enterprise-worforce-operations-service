# Comedores — Modelo de dominio (borrador)

> **Consultar cuando:** se modelan entidades, atributos o su relación con el modelo organizativo del punto 8.
> **Certeza:** los atributos y relaciones provienen de las pantallas y textos del documento fuente (*hecho del documento*). La correspondencia con los términos del punto 8 y los tipos de datos son **inferidos**. No es un esquema de base de datos ni fija nombres de clases.

## Entidades propias del módulo

| Entidad | Atributos observados | Notas |
|---|---|---|
| **Tipo de servicio** | Código, descripción, observación | Valores iniciales: desayuno, almuerzo, cena |
| **Comedor** | Código, descripción, tipo de control, tipo de comedor, controlar check-in | Puede haber N. Lo normal en la fuente es un comedor principal y otro adicional |
| **Servicio de comedor** (un tipo de servicio dentro de un comedor) | Moneda, monto, hora de inicio, hora de fin, servicio especial, validar sin conexión, consumo máximo, servicio excluyente | Es el lugar de la configuración funcional (precio, horario, límites) |
| **Acceso al comedor** | Persona (o compañía), comedor, servicio, hora de inicio y fin, fecha de inicio y fin, días de la semana (lunes a domingo), centro de costo, modo de modificación | Vigencia por fechas y días |
| **Restricción de servicio** | Persona, comedor, servicio, fecha de inicio y fin (o definitiva), observaciones | Prima sobre el acceso |
| **Consumo** (evento) | Persona, comedor, servicio, fecha y hora, centro de costo, precio, moneda, cantidad, subtotal, equipo (tótem), origen (tótem, manual, importación) | Es un evento de consumo (§4: semántica propia) |
| **Consumo negado** | Persona o credencial, comedor, servicio, fecha y hora, centro de costo, motivo | Ejemplos de motivo vistos en la fuente: persona o credencial inexistente; sin servicio asignado. La lista completa no está definida |
| **Marcación manual / importación** | Tipo de persona, código de trabajador, nombre, documento de identidad, comedor, servicio, cantidad, equipo | Contingencia |
| **Habilitación de compañía para comedor** | Compañía, indicador "autorizada para hacer uso de los comedores" | Condicionada a la relación contractual vigente |

## Conceptos que el módulo consume de otros dominios

| Concepto | Dónde se usa | Correspondencia con el punto 8 (inferida) |
|---|---|---|
| Compañía / empresa contratista | Habilitación, acceso, reportes agrupados | **Contratista** de D8-03. Las compañías también incluyen a la propia minera, que sería **empresa mandante** |
| Trabajador / persona | Acceso, restricción, consumo | **Persona** más **vínculo** con su empleador (D8-03). Estado activa/inactiva visto en pantalla |
| Credencial (fotocheck) | Marcar en el tótem | **Credencial** del módulo de identidades; el consumo referencia la **identidad operativa** (R8-17) |
| Tótem | Emite el ticket y registra la marcación | Terminal; se integra por adaptador (R8-50) |
| Unidad (Lima, Cañete) | Alcance del proyecto | Probablemente **sedes**; los comedores serían **puntos operativos** de una sede (R8-19). *No confirmado* |
| Requisitos de acreditación | Condicionan el ingreso | Elegibilidad del módulo de acreditación (no se modela aquí) |

## Conceptos que **no existen** en el modelo organizativo aprobado

Aparecen en reportes o filtros de la fuente y D8-03 no los define. Hasta decidirlo no deben inventarse:

- **Grupo** (por ejemplo administración, mina, operaciones): agrupa compañías o áreas en los filtros y reportes.
- **Centro de costos**: aparece en el acceso asignado, en el consumo y en el pedido de un resumen consolidado.
- **Categoría del trabajador** (empleado, obrero, practicante) y **cargo**: el cliente pide incluirlos en el detalle de consumo.
- **Área** del trabajador y **código personal**: columnas solicitadas por el cliente.

Ver [08-preguntas-abiertas.md](08-preguntas-abiertas.md).

## Relaciones principales

- Un **comedor** tiene varios **servicios de comedor**, cada uno de un **tipo de servicio**.
- Una **persona** (en su vínculo) recibe **accesos** a servicios de comedores y puede tener **restricciones**.
- Un **consumo** referencia la identidad operativa, el servicio de comedor y guarda el contexto del momento: comedor, servicio, mandante, contratista, centro de costo, precio y moneda vigentes (R8-48; el precio vigente es inferido).
- Una **compañía** debe estar habilitada para que se asignen accesos a su personal.

## Reglas de datos heredadas del punto 8

- Todo está acotado al tenant; las unicidades (por ejemplo código de comedor) son **por tenant** (D8-02, R8-05).
- Los registros de consumo no se reescriben cuando cambia el vínculo o el precio (R8-16, R8-48).
- Ningún consumo se descarta; los que no encajan se guardan con su resultado (R8-53).
