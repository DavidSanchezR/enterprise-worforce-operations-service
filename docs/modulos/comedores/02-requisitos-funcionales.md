# Comedores — Requisitos funcionales (RF-COM)

> **Consultar cuando:** se planifica o implementa una funcionalidad concreta.
> **Certeza:** la columna *Origen* indica la sección del documento fuente. Los criterios de aceptación son **propuestos** por quien redactó (inferidos), no del cliente; requieren validación. Prioridad y fase se definen en [07-backlog-y-dependencias.md](07-backlog-y-dependencias.md).
> **Alcance de esta etapa (declarado 2026-10-30, ver [D-ALC-01](../../decisiones/D-ALC-01-alcance-etapa-comedores.md)):** quedan **fuera** todos los reportes, el horario de interfaces, la migración de datos y los consumos manuales (incluida su carga masiva e importación de marcaciones).
> **Estado de cada RF:** *Confirmado* (el cliente está conforme), *Por confirmar* (hay contradicción o falta información) o *Fuera de alcance*.

## Catálogos y configuración

| ID | Requisito | Origen | Estado |
|---|---|---|---|
| RF-COM-01 | Gestionar **tipos de servicio**: crear, modificar y desactivar. Servicios iniciales: desayuno, almuerzo y cena | 4.1 | Confirmado |
| RF-COM-02 | Gestionar **comedores** (N comedores) y asociar a cada uno los servicios que brinda. Por cada servicio de un comedor se configura: precio (moneda y monto), horario de inicio y fin, si es servicio especial, si se valida sin conexión, consumo máximo y si es excluyente. Cada comedor tiene un tipo de control y un tipo de comedor, y la opción de controlar check-in | 4.2 | Confirmado |
| RF-COM-03 | Horario de interfaces | 4.3 | **Fuera de alcance** (no se usa; los horarios de atención ya están establecidos) |

## Habilitación y acceso

| ID | Requisito | Origen | Estado |
|---|---|---|---|
| RF-COM-04 | **Habilitar a una empresa contratista** para usar el comedor. Solo si existe relación contractual vigente (contrato, orden de servicio u otro documento vinculante) | 3.1 | Confirmado |
| RF-COM-05 | **Asignar acceso al comedor por persona**, individual y masivo: comedor, servicio, horas de inicio y fin, fechas de inicio y fin, días de la semana y modo de modificación (actualizar fechas de todos los comedores asignados o solo el registro seleccionado). Se consulta por estado (activas, inactivas, todas) | 3.2, 4.4 | Confirmado |
| RF-COM-06 | **Asignar acceso por compañía**: una asignación aplicable a la empresa. Hoy no se usa; se contempla una opción similar | 4.5 | Por confirmar (semántica) |
| RF-COM-07 | **Restringir el servicio** a un trabajador de forma definitiva o por rango de fechas, con comedor, servicio y observaciones | 4.6 | Confirmado |

## Consumo

| ID | Requisito | Origen | Estado |
|---|---|---|---|
| RF-COM-08 | **Registrar el consumo** cuando la persona marca en un tótem: se valida su permiso y el horario del servicio, y se emite un ticket con los datos del servicio | 2.1 | Confirmado (inferido del flujo y las definiciones) |
| RF-COM-09 | **Registrar consumos denegados** con su motivo. El registro es obligatorio (R8-53); su consulta en pantalla queda como RF-COM-14 | 4.16 | Confirmado (el reporte de negados queda fuera) |
| RF-COM-10 | **Registrar consumos manuales** como contingencia (por ejemplo caída de energía), con carga masiva desde Excel | 4.7 | **Fuera de alcance** (declarado por el usuario) |
| RF-COM-11 | **Importar marcaciones manuales** (persona, comedor, servicio, cantidad, equipo), si se requiere | 4.8 | **Fuera de alcance** (declarado por el usuario) |
| RF-COM-12 | **Imprimir ticket de consumo** en los tres servicios | 5.1 | Confirmado (el cliente lo pidió de nuevo); hardware por definir |

## Consulta y reportes

| ID | Requisito | Origen | Estado |
|---|---|---|---|
| RF-COM-13 | **Consultar consumo agrupado por compañía** (reporte con datos de las compañías) | 4.9 | **Fuera de alcance** (es un reporte) |
| RF-COM-14 | **Consultar consumo por persona** | 4.10 | Por confirmar: se asume una consulta en pantalla, no un reporte; sin ella no se puede verificar el módulo |
| RF-COM-15 | **Reportes** (acceso, consumo, consolidado, detalle, resumen mensual, negados) | 4.11 a 4.16 | **Fuera de alcance** (ver [05-reportes.md](05-reportes.md)) |

## Datos

| ID | Requisito | Origen | Estado |
|---|---|---|---|
| RF-COM-16 | **Migrar trabajadores**, incluido el personal cesado | 5.3 | **Fuera de alcance** (declarado por el usuario) |
| RF-COM-17 | **Migrar el historial** de proyecto | 5.4 | **Fuera de alcance** (declarado por el usuario) |
| RF-COM-18 | **Acceso del personal propio** (practicante, obrero, empleado): si usa todos los comedores y los tres servicios sin restricción | 5.6 | Por confirmar (respuesta cortada) |

## Criterios de aceptación propuestos (núcleo del módulo)

Son borradores para validar con el cliente; no están en el documento fuente.

**RF-COM-05 Asignar acceso por persona**
- *Dado* una persona de una contratista habilitada, *cuando* se le asigna un servicio de un comedor con fechas y días, *entonces* puede consumir solo dentro de esas fechas, días y horario.
- *Dado* una asignación masiva a varias personas, *cuando* una falla por regla de negocio, *entonces* las demás se procesan y el resultado informa cuáles fallaron y por qué.
- *Dado* una contratista no habilitada (RF-COM-04), *cuando* se intenta asignar acceso a su personal, *entonces* se rechaza con el motivo.

**RF-COM-07 Restricción**
- *Dado* una restricción vigente para un servicio, *cuando* la persona marca, *entonces* se deniega y se registra el consumo denegado con el motivo "restricción".
- *Dado* una restricción por rango, *cuando* termina el rango, *entonces* la persona recupera el acceso sin intervención.

**RF-COM-08 / 09 Consumo y denegación**
- *Dado* una persona con acceso vigente en el horario del servicio, *cuando* marca, *entonces* se registra el consumo con el contexto del momento (comedor, servicio, contratista, precio vigente) y se emite el ticket.
- *Dado* una marcación sin permiso, fuera de horario o de una credencial desconocida, *cuando* se procesa, *entonces* queda registrada como denegada con su motivo; nunca se descarta (R8-53).
- *Dado* un tenant con la licencia restringida tras la gracia, *cuando* llega un consumo, *entonces* se guarda en cuarentena sin descartarse (R8-12, R8-22).

*(Los criterios del consumo manual se retiraron: RF-COM-10 está fuera de alcance en esta etapa.)*
