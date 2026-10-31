# D-ALC-01 — Alcance de la etapa actual: módulo Comedores

> **Estado:** declarada por el usuario el 2026-10-30. No es una decisión de arquitectura aprobada con alternativas; es el recorte de alcance que ordena el trabajo.
> **Relacionada con:** [D-FE-01](D-FE-01-frontend-blazor-server.md), [informe de brechas](../integracion/informe-brechas-eac-vs-comedores.md), [módulo Comedores](../modulos/comedores/README.md).

## Qué entra

- El módulo de **Comedores** sobre la solución existente (EAC), con las reglas de **tenant y licenciamiento completas** del punto 8: aislamiento, entitlement con límites, gracia, cuarentena, permisos y alcance, configuración con herencia, auditoría atómica y contrato de ciclo de vida de datos.
- Interfaz en **Blazor Server** para toda la solución: se **reconstruyen en Blazor las pantallas de Etapa 1** y se retira la SPA React (ver [D-FE-01](D-FE-01-frontend-blazor-server.md)).
- Integración de todo lo generado (decisiones D8-01 a D8-05, requisitos R8, documentos del módulo) en la solución existente.

## Qué no entra

| Excluido | Detalle |
|---|---|
| Reportes | Ningún reporte ni exportación del módulo (RF-COM-13, 15 y REP-COM-01 a 08) |
| Horario de interfaces | RF-COM-03 |
| Migraciones | Trabajadores, cesados e historial desde el producto de escritorio (RF-COM-16, 17) |
| Consumos manuales | Registro manual, carga masiva e importación de marcaciones (RF-COM-10, 11) |
| Asistencia | Time & Attendance queda para otra etapa; tampoco se amplían acceso físico ni acreditación |

## Consecuencias

- Quedan sin efecto las preguntas Q-01, Q-03, Q-04, Q-05, Q-06 y Q-08.
- Se retiran los permisos de consumo manual y de reportes del catálogo propuesto del módulo.
- Como "todas las reglas de tenant y licenciamiento" aplican, la plataforma mínima debe incluir gracia y cuarentena, no solo el entitlement.
- Se asume que RF-COM-14 (consulta por persona en pantalla) sigue, por ser necesaria para verificar el módulo; **por confirmar** (Q-27).

## Pendiente de decisión

Frontera del módulo (I-3), qué es un comedor en el modelo (I-4), enmienda de la constitución (I-5), roles y permisos (I-6), esquema de autenticación de la UI Blazor (Q-26) y qué hacer con los endpoints HTTP de la UI (Q-28). Ver el informe de brechas.
