# Comedores — Backlog y dependencias

> **Consultar cuando:** se planifica el trabajo o se decide si se puede empezar a implementar.
> **Certeza:** la secuencia y la agrupación son **propuestas** (inferidas). No hay estimaciones de esfuerzo. Ninguna fase de implementación está autorizada hasta cumplir la fase 0.
> **Alcance (declarado 2026-10-30, [D-ALC-01](../../decisiones/D-ALC-01-alcance-etapa-comedores.md)):** sin reportes, horario de interfaces, migraciones ni consumos manuales. Tenant y licenciamiento aplican completos. La solución existente (EAC) ya está analizada: ver [informe de brechas](../../integracion/informe-brechas-eac-vs-comedores.md).

## Fase 0 — Prerrequisitos (no son código del módulo)

| # | Prerrequisito | Estado conocido |
|---|---|---|
| P0-1 | Stack | **Verificado en el repositorio:** .NET 10, C# 13, ASP.NET Core 10, EF Core 10, SQL Server, FluentValidation 12, NodaTime, JWT. La UI nueva es Blazor Server |
| P0-2 | Estado del repositorio | Analizado en lectura (EAC Etapa 1 cerrada). Falta verificar Git, pruebas y frontend |
| P0-3 | Frontera de Comedores y ubicación de Platform | Propuesta (B: proyectos propios); **sin decidir** (I-3 del informe) |
| P0-4 | Núcleo organizativo mínimo: tenant, sede, punto operativo, y los conceptos de Q-16 sobre lo existente (compañía, persona, pertenencia, credencial) | Parcialmente existente; brechas en el informe |
| P0-5 | Plataforma completa para el módulo: contexto de tenant, entitlement con límites, gracia y cuarentena, permisos y alcance revalidados por operación, configuración con herencia, auditoría atómica | No existe (B-01 a B-06) |
| P0-6 | Cerrar las preguntas bloqueantes (ver [08-preguntas-abiertas.md](08-preguntas-abiertas.md)) | Abiertas |
| P0-7 | Validación legal de conservación, bloqueo y eliminación | Pendiente |
| P0-8 | Base Blazor Server: host, cliente HTTP tipado hacia la API, token por circuito y alcance resuelto por la API en cada solicitud | Diseño pendiente ([D-FE-01](../../decisiones/D-FE-01-frontend-blazor-server.md)) |
| P0-9 | Pantallas de Etapa 1 que Comedores consume (inicio de sesión, compañías, personas, credenciales) reconstruidas en Blazor | Por construir |

## Fase 0b — Base Blazor y pantallas de Etapa 1

Host Blazor Server con autenticación única y contexto por circuito (P0-8), y reconstrucción de las pantallas de Etapa 1 empezando por las que Comedores consume (P0-9). El resto de pantallas (unidades, áreas, permisos, evaluación, datos maestros, usuarios y roles) se migra después; React se retira al verificar paridad.

## Fase 1 — Catálogos y habilitación

| Historia | RF | Depende de |
|---|---|---|
| Gestionar tipos de servicio | RF-COM-01 | P0-1, P0-5 |
| Gestionar comedores y servicios de comedor | RF-COM-02 | P0-4, P0-5 |
| Habilitar y deshabilitar contratistas | RF-COM-04 | Compañías y relación contratista–principal existentes |

## Fase 2 — Acceso y restricciones

| Historia | RF | Depende de |
|---|---|---|
| Asignar acceso por persona, individual y masivo | RF-COM-05 | Fase 1; identidad operativa (credencial existente) |
| Asignar acceso por compañía | RF-COM-06 | Fase 1; semántica por aclarar (Q-09) |
| Restricciones definitivas y por rango | RF-COM-07 | RF-COM-05 |
| Acceso del personal propio | RF-COM-18 | Q-02 |

## Fase 3 — Consumo

| Historia | RF | Depende de |
|---|---|---|
| Ingestión de marcaciones desde el tótem por adaptador y contrato | RF-COM-08, RF-COM-09 | Fase 2; modelo de evento; identidad de dispositivo; capacidades por modelo (Q-22) |
| Registro de consumos denegados con motivo | RF-COM-09 | RF-COM-08 |
| Ciclo de vida de licencia: cuarentena y consumos observados | RN-COM-13, RN-COM-14 | Plataforma (P0-5); tensión de offline resuelta (Q-18) |
| Ticket de consumo | RF-COM-12 | Hardware por definir (Q-07) |
| Consulta en pantalla por persona | RF-COM-14 | Fase 3; por confirmar (Q-27) |

## Fuera de esta etapa

Reportes y exportaciones (RF-COM-13, 15), horario de interfaces (RF-COM-03), consumos manuales, carga masiva e importación de marcaciones (RF-COM-10, 11) y migración de trabajadores e historial (RF-COM-16, 17). Asistencia y el resto de módulos de EWOP tampoco.

## Transversal en cada fase

- Pruebas de aislamiento entre tenants, mandantes y contratistas, y de fuga entre compañías (ya hay un patrón en EAC: CS-037).
- Pruebas de entitlement y permiso por cada endpoint y por cada operación de la UI.
- Clasificación de operaciones declarada por caso de uso.
- Contrato de ciclo de vida (bloqueo, exportación, eliminación) probado.
- Terminales sin acceso directo a la BD.

## Orden recomendado (a validar)

1. Cerrar la fase 0 y las preguntas bloqueantes, y pasar por Spec Kit: enmienda de la constitución, especificación de plataforma y especificación de Comedores (ver el informe de brechas).
2. Construir una **rebanada vertical mínima**: un tipo de servicio, un comedor, una asignación de acceso y un consumo que llega de un tótem simulado, de punta a punta con tenant, entitlement y permiso, sobre una pantalla Blazor Server (la base Blazor ya construida).
3. Ampliar por fases.
