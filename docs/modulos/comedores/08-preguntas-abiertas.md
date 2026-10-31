# Comedores — Preguntas abiertas

> **Consultar cuando:** antes de implementar, para saber qué falta aclarar.
> **Cerradas por alcance (declarado 2026-10-30):** Q-01, Q-03, Q-04, Q-05, Q-06 y Q-08 ya no aplican porque los reportes, la migración y los consumos manuales quedan fuera de esta etapa. Se conservan por si se retoman.
> **Certeza:** nada de lo siguiente está decidido. "Bloqueante" significa que impide diseñar o implementar la parte indicada.

## Con el cliente (contenido del documento fuente)

| # | Pregunta | Impacto | Bloqueante |
|---|---|---|---|
| ~~Q-01~~ (fuera de alcance) | Los reportes de acceso, consumo y consolidado figuran como "no se considera", pero el cliente pide una versión (resumen por centro de costos, o Excel). ¿Entran en el alcance? | REP-COM-03, 04, 05 | Sí, para esos reportes |
| Q-02 | ¿Qué respondió el cliente en 5.6 sobre el acceso del personal propio (practicante, obrero, empleado)? El comentario está cortado en el PDF | RF-COM-18 | Sí, para la regla de acceso del personal propio |
| ~~Q-03~~ (fuera de alcance) | ¿Qué abarca "incluir historial de proyecto" en la migración: todo el histórico o desde una fecha de corte? | RF-COM-17 | Sí, para la migración |
| ~~Q-04~~ (fuera de alcance) | ¿Cuántos años o cuántos registros tiene el histórico, y en qué formato está el origen? | RF-COM-16, 17 | No |
| ~~Q-05~~ (fuera de alcance) | ¿La columna DNI, Área, código personal también aplica al resumen mensual? El comentario aparece duplicado | REP-COM-06, 07 | No |
| ~~Q-06~~ (fuera de alcance) | ¿Se usa el resumen mensual? El documento dice que el área debe definirlo | REP-COM-07 | No |
| Q-07 | ¿Qué hardware imprime los tickets, quién provee los consumibles y qué contiene el ticket? | RF-COM-12 | Sí, para el ticket |
| ~~Q-08~~ (fuera de alcance) | ¿Se necesita importar marcaciones manuales además de la carga masiva de consumos? | RF-COM-11 | No |
| Q-09 | ¿Qué significa asignar el acceso "por compañía"? ¿Aplica a todo su personal vigente o solo a quienes se asignen después? | RF-COM-06 | Sí, para esa funcionalidad |

## Semántica del dominio

| # | Pregunta | Impacto | Bloqueante |
|---|---|---|---|
| Q-10 | ¿Qué significan "tipo de control", "tipo de comedor", "controlar check-in", "servicio especial" y "servicio excluyente"? | Modelo y reglas | Sí |
| Q-11 | El "consumo máximo", ¿es por persona y por día, por turno o por comedor? | RN-COM-08 | Sí |
| Q-12 | Lista completa de motivos de consumo denegado | RF-COM-09 | Sí |
| Q-13 | ¿Hay subsidios, cuotas, cobro a contratistas o descuentos? El documento muestra precio, moneda, cantidad y subtotal pero no explica quién paga | Reglas de precio, reportes | Sí, si hay cobro |
| Q-14 | ¿Se necesita más de una moneda? La fuente tiene campo de moneda | Modelo | No |
| Q-15 | ¿Cómo se aplica una restricción con respecto a un acceso asignado (siempre prevalece la restricción)? | RN-COM-04 | No, es una inferencia |

## Con el punto 8 y el modelo organizativo

| # | Pregunta | Impacto | Bloqueante |
|---|---|---|---|
| Q-16 | **Grupo**, **centro de costos**, **categoría del trabajador**, **cargo**, **área** y **código personal** no existen en D8-03. ¿Dónde viven y quién es su dueño (núcleo organizativo, vínculo, otro)? | Modelo, reportes, contexto del momento | Sí |
| Q-17 | ¿El comedor es un punto operativo de una sede? ¿Las unidades (Lima, Cañete) son sedes? | Configuración, alcance | Sí |
| Q-18 | La fuente configura "validar sin conexión" por servicio, y R8-51 dice que el comedor no valida offline rango horario ni comedor. ¿Qué valida exactamente el tótem sin conexión? | RN-COM-13, adaptador | Sí, para la lógica offline |
| Q-19 | Despliegue de CMC: la fuente habla de Azure. ¿CMC es un tenant del SaaS o una instalación dedicada operada por el proveedor? | D8-01 | No para el módulo |
| Q-20 | ¿Cuál es la sanción o el costo adicional ante un consumo observado (exceso)? R8-51 lo menciona como regla posible | RN-COM-13 | Sí, si se aplica |
| Q-21 | ¿El personal propio de la minera y las contratistas se tratan igual en el comedor (mandante frente a contratista)? | Reglas de acceso | No |

## Técnicas

| # | Pregunta | Impacto | Bloqueante |
|---|---|---|---|
| Q-22 | Modelos y firmware de tótems; capacidades de memoria local, impresión y modo sin conexión | Adaptadores, RN-COM-13 | Sí, para la ingestión |
| Q-23 | Volumen de consumos por día y picos en horario de comida | Dimensionamiento, cuarentena | No |
| Q-24 | Librerías de UI, exportación Excel y PDF, y pruebas de UI en Blazor Server | D-FE-01 | No |
| Q-25 | Stack completo y estado real del repositorio | Stack **verificado** (.NET 10, EF Core 10, SQL Server); falta Git, pruebas y frontend | Parcial |
| Q-26 | Cómo obtiene y renueva el host Blazor el token del usuario hacia la API dentro del circuito, y cómo resuelve la API el alcance por solicitud | D-FE-01, B-02 | Sí |
| ~~Q-28~~ | **Cerrada 2026-10-30:** la UI consume la API por HTTP; se reutilizan o adaptan los endpoints posibles y el resto se retira a carpeta fuera de la solución | D-FE-01 | — |
| Q-29 | ¿La API queda pública o solo en red interna detrás del host Blazor? (afecta el objetivo anticopia) | D-FE-01, D8-01 | Sí, antes del despliegue |
| Q-30 | Ubicación de la carpeta de retiro fuera de la solución (propuesta: `archivo/` en la raíz del repositorio) | D-FE-01 | No |
| Q-27 | ¿La consulta por persona (RF-COM-14) sigue en alcance como consulta en pantalla? | RF-COM-14 | No |

## Cómo se cierran

Las preguntas con el cliente (Q-01 a Q-09) se resuelven en una sesión de validación con él. Las de modelo (Q-10 a Q-17) se resuelven en el diseño del núcleo organizativo y del módulo. Las técnicas (Q-22 a Q-25) requieren verificar hardware y repositorio. Al cerrar una pregunta, actualizar este archivo y el requisito afectado.
