# Orden de ejecución — migración a Blazor Server y desarrollo de Comedores

> **Consultar cuando:** se pregunta "¿qué sigue?". Es la hoja de ruta; los textos de los prompts están en [prompts-de-arranque.md](prompts-de-arranque.md) y [prompts-migracion-pantallas.md](prompts-migracion-pantallas.md).
> **Certeza:** propuesta. Las duraciones no se estiman porque no hay datos del equipo ni del tamaño real del código.
> **Decisiones declaradas que lo condicionan:** toda la UI en Blazor Server consumiendo la API por HTTP; se reutilizan o adaptan los endpoints posibles y el resto se retira a una carpeta fuera de la solución; tenant y licenciamiento completos; Comedores sin reportes, horario de interfaces, migraciones ni consumos manuales.

## Cuándo abrir VS Code

**Ya puedes abrirlo.** Los documentos y el `CLAUDE.md` están en tu solución. Desde el paso 0 todo se hace allí con Claude Code, porque necesita ejecutar Git, compilar y correr pruebas, cosa que esta sesión no puede hacer. Si Claude Code ya estaba abierto en esa carpeta, ciérralo y ábrelo de nuevo para que lea el `CLAUDE.md`.

Vuelve a esta conversación cuando haya que **decidir** algo (marcado con "Decisión" abajo) o para revisar un informe.

## Fase A — Preparación (solo lectura y papeles)

| # | Qué | Dónde | Punto de control |
|---|---|---|---|
| 0 | Rama y commit de los documentos | VS Code, a mano | `git status` limpio tras el commit |
| 1 | Línea base: Git, compilación, pruebas, inventario de pantallas | Claude Code | Informe aprobado; si las pruebas no pasan desde una base limpia, se decide qué hacer antes de seguir |
| 2 | Inventario de endpoints y pruebas | Claude Code | **Decisión:** apruebas o corriges la clasificación (reutilizar, adaptar, retirar) |
| 3 | Enmienda de la constitución | Claude Code | **Decisión:** apruebas el texto |

## Fase B — Base técnica

| # | Qué | Dónde | Punto de control |
|---|---|---|---|
| 4 | Especificación 002: base Blazor | Claude Code | **Decisión:** Q-26 y Q-29 (API pública o interna) |
| 5 | Implementar 002 | Claude Code | Host Blazor funcionando; cambio de alcance aplicado a un circuito abierto |
| 6 | Especificación 003: plataforma (tenant, licencias, permisos, auditoría) | Claude Code | **Decisión:** I-3, I-6 y cómo entra `TenantId` |
| 7 | Implementar 003 | Claude Code | Pruebas de aislamiento y licencias en verde; **apruebas las migraciones antes de aplicarlas** |

Por qué la plataforma va antes que las pantallas: la pantalla de usuarios y roles depende del modelo de permisos, y la introducción del tenant cambia los contratos de la API. Migrar pantallas antes obligaría a rehacerlas.

## Fase C — Migración de pantallas

| # | Qué | Dónde | Punto de control |
|---|---|---|---|
| 8 | Especificación 004 | Claude Code | Criterios de paridad aprobados |
| 9a | M0: piloto (navegación, inicio de sesión, cambio de contraseña) | Claude Code | Patrones documentados |
| 9b | M1 por grupo: datos maestros, compañías, personas con historial y credenciales, usuarios y roles, unidades, áreas, permisos, evaluación | Claude Code, una sesión por grupo | Fila de `paridad-pantallas.md` actualizada con evidencia |
| 9c | M2: verificación independiente por grupo | Claude Code, sesión limpia | **Decisión:** pasas la fila a "Paridad verificada" |

## Fase D — Comedores

| # | Qué | Dónde | Punto de control |
|---|---|---|---|
| 10 | Especificación 005 | Claude Code | **Decisión:** preguntas bloqueantes (Q-02, Q-10, Q-11, Q-16, Q-17, Q-18, Q-22) e I-4; varias requieren al cliente |
| 11 | Rebanada vertical y luego fases del backlog | Claude Code | Pruebas en verde; rebanada demostrable |

**Atajo opcional (decides tú):** Comedores solo necesita inicio de sesión, navegación, datos maestros, compañías, personas y credenciales. Podría empezar al verificar esos grupos, sin esperar a unidades, áreas, permisos y evaluación. Tú pediste migrar primero; el atajo ahorra tiempo pero mantiene React vivo más tiempo.

## Fase E — Retiro

| # | Qué | Dónde | Punto de control |
|---|---|---|---|
| 12 | Mover React, y los endpoints y pruebas marcados "retirar", a una carpeta fuera de la solución | Claude Code | **Decisión:** Q-30 (ubicación), y apruebas cada comando de Git; etiqueta previa |

## Qué puede bloquearte desde el principio

- **Entorno:** SQL Server en Docker para las pruebas de integración. El README advierte que la suite E2E no es reproducible desde una base limpia.
- **Preguntas al cliente:** varias preguntas de Comedores (Q-02, Q-10, Q-11, Q-18) dependen de él; conviene pedírselas en paralelo mientras avanzan las fases A a C.
- **Validación legal** de retención, bloqueo y eliminación (R8-27, R8-42): pendiente, afecta al contrato de ciclo de vida de la plataforma.
