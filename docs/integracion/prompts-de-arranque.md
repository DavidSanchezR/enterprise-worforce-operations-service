# Prompts de arranque — secuencia de trabajo con Claude Code

> **Consultar cuando:** se va a empezar o continuar el desarrollo. Cada prompt se pega en Claude Code, dentro del repositorio, donde ya se lee `CLAUDE.md`. El orden y los puntos de control están en [orden-de-ejecucion.md](orden-de-ejecucion.md).
> **Certeza:** propuesta. Las decisiones abiertas (I-3 a I-6, Q-26, Q-29, Q-30) las propone Claude en cada paso y **solo se dan por cerradas con confirmación explícita del usuario**.
> **Regla general:** un prompt por sesión; no se pasa al siguiente sin cumplir el criterio de terminado.

## Paso 0 — Preparación manual (la haces tú en VS Code)

Los documentos ya están en el repositorio como archivos nuevos. Antes de nada, guárdalos en una rama propia. Los comandos de Git (cada uno con su efecto):

- `git status`: muestra qué cambió; deberías ver `CLAUDE.md` y `docs/` como archivos nuevos y nada más.
- `git switch -c docs/integracion-comedores`: crea una rama nueva y te pasa a ella, para no tocar la rama principal.
- `git add CLAUDE.md docs`: prepara solo esos archivos para guardarlos.
- `git commit -m "docs: integra contexto EWOP, punto 8 y modulo Comedores"`: los guarda en la rama.

Si `git status` muestra otros cambios, no los incluyas y avísame o revísalos antes.

## Paso 1 — Línea base (solo lectura)

**Prompt:** Lee `CLAUDE.md` y `docs/integracion/informe-brechas-eac-vs-comedores.md`. Sin modificar ningún archivo: ejecuta `git status` y `git log -10`, compila la solución, ejecuta las suites de unidad, integración y contrato, e inventaría las pantallas de `frontend/src/features` y sus pruebas E2E. Explica cada comando antes de usarlo.
**Terminado cuando:** hay un informe con el estado real de Git, resultado de compilación y pruebas, y la lista de pantallas. Se corrige el informe de brechas en lo que difiera.
**No hacer:** cambiar código, instalar dependencias globales ni borrar nada.

## Paso 2 — Inventario de endpoints y pruebas (solo lectura)

**Prompt:** Lee `docs/decisiones/D-FE-01-frontend-blazor-server.md` (sección de destino de endpoints) y los controladores de `backend/src/EnterpriseAccessControl.Api`. Genera `docs/integracion/inventario-endpoints.md` con una fila por endpoint: ruta y método, controlador, servicio de aplicación que invoca, pruebas de contrato e integración que lo cubren, consumidor previsto (UI Blazor, terminal o ninguno) y clasificación propuesta: **reutilizar tal cual**, **adaptar** (tenant, permisos por módulo, alcance por solicitud) o **retirar**, con el motivo. Haz lo mismo con las pruebas.
**Terminado cuando:** el inventario cubre los 72 endpoints y el usuario lo aprueba o lo corrige.
**No hacer:** modificar o mover nada; decidir por el usuario los casos dudosos (márcalos).

## Paso 3 — Enmienda de la constitución

**Prompt:** Usa `/speckit-constitution`. Lee `docs/decisiones/D-FE-01-frontend-blazor-server.md`, `docs/multitenancy/D8-02`, `D8-04` y `D8-05`. Propón el texto de una enmienda MINOR (1.2.0) que incorpore: contexto de tenant derivado de la identidad, frontera entre módulos, auditoría atómica de operaciones, alcance y permiso resueltos por solicitud (no solo por token) y Blazor Server consumiendo la API por HTTP. Muéstrame el texto antes de escribirlo.
**Terminado cuando:** el usuario aprueba el texto y la constitución queda versionada con su informe de impacto.
**No hacer:** eliminar o debilitar ningún principio vigente.

## Paso 4 — Especificación 002: base Blazor Server

**Prompt:** `/speckit-specify` para "Base Blazor Server": host Blazor, inicio de sesión único, cliente HTTP tipado hacia la API (evalúa generarlo desde los contratos OpenAPI de `specs/001-…/contracts`), obtención y renovación del token por circuito, y alcance resuelto por la API en cada solicitud. Luego `/speckit-clarify`; presenta como opciones con recomendación Q-26 y Q-29 (API pública o interna).
**Terminado cuando:** `specs/002-…/spec.md` sin ambigüedades abiertas y con un criterio de aceptación que pruebe que un cambio de asignaciones se aplica a un circuito abierto.
**No hacer:** implementar; tocar `specs/001-…`.

## Paso 5 — Implementar la especificación 002

**Prompt:** Ejecuta `/speckit-plan`, `/speckit-tasks`, `/speckit-analyze` y, tras mi aprobación del análisis, `/speckit-implement` para `specs/002-…`. Respeta la regla de pruebas obligatorias y explica cada comando de Git o de base de datos antes de usarlo.
**Terminado cuando:** el host Blazor arranca, se inicia sesión a través de la API, el cambio de alcance se aplica a un circuito abierto y todas las suites pasan.
**No hacer:** migrar pantallas todavía; romper pruebas existentes.

## Paso 6 — Especificación 003: plataforma

**Prompt:** `/speckit-specify` para la plataforma: tenant, entitlement con límites, gracia y cuarentena, permisos y alcance, configuración con herencia, auditoría atómica y contrato de ciclo de vida. Lee `docs/multitenancy/` completo, el informe de brechas (B-01 a B-10) y `inventario-endpoints.md`. Propón la frontera del módulo (I-3), el modelo de roles y permisos (I-6) y cómo se introduce `TenantId` en las entidades existentes con un tenant por defecto.
**Terminado cuando:** spec aprobada y plan (`/speckit-plan`) con las migraciones descritas, sin ejecutarlas.
**No hacer:** reutilizar `EvaluadorDeAcceso` para consumos; resolver en silencio los puntos legales (retención y eliminación).

## Paso 7 — Implementar la especificación 003

**Prompt:** Igual que el paso 5, para `specs/003-…`. Antes de ejecutar migraciones de base de datos, muéstrame el script y su efecto.
**Terminado cuando:** pruebas de aislamiento entre tenants, entitlement, permiso por módulo, gracia y cuarentena en verde; contratos de API adaptados según el inventario.
**No hacer:** aplicar migraciones sin mi aprobación.

## Paso 8 — Especificación 004: pantallas de Etapa 1 en Blazor

**Prompt:** `/speckit-specify` para reconstruir en Blazor Server las pantallas de Etapa 1, por historias independientes, a partir de `docs/integracion/paridad-pantallas.md`, el inventario del paso 1 y `specs/001-…/ux-ui.md`.
**Terminado cuando:** cada historia tiene criterios de paridad y pruebas de UI definidas.
**No hacer:** borrar código React ni endpoints.

## Paso 9 — Migración de pantallas

Se ejecuta con [prompts-migracion-pantallas.md](prompts-migracion-pantallas.md): piloto M0, luego M1 por grupo y M2 de verificación. Es repetitivo: una sesión por grupo.

## Paso 10 — Especificación 005: Comedores

**Prompt:** `/speckit-specify` para Comedores a partir de `docs/modulos/comedores/` (README, 02, 03, 04, 06 y 08). Alcance según `docs/decisiones/D-ALC-01-alcance-etapa-comedores.md`. Antes de especificar, enumera las preguntas bloqueantes (Q-02, Q-10, Q-11, Q-16, Q-17, Q-18, Q-22) y pídelas al usuario; propón qué es un comedor en el modelo (I-4).
**Terminado cuando:** spec aprobada con las preguntas bloqueantes cerradas o con supuestos aceptados por escrito.
**No hacer:** incluir reportes, horario de interfaces, migraciones ni consumos manuales; copiar pantallas del producto original.

## Paso 11 — Desarrollo de Comedores

**Prompt:** `/speckit-plan`, `/speckit-tasks` y `/speckit-analyze` para `specs/005-…`, y luego `/speckit-implement` solo para la rebanada vertical: un tipo de servicio, un comedor, una asignación de acceso y un consumo recibido de un tótem simulado, con tenant, entitlement, permiso y auditoría, sobre una pantalla Blazor. Después, las fases del backlog, una por sesión.
**Terminado cuando:** pruebas de aislamiento, entitlement, permiso y cuarentena en verde y la rebanada demostrable de punta a punta.
**No hacer:** ampliar funcionalidad antes de cerrar la rebanada.

## Paso 12 — Retiro de React y de lo no reutilizable

Prompt M3 de [prompts-migracion-pantallas.md](prompts-migracion-pantallas.md). Mueve a una carpeta fuera de la solución, con etiqueta de Git previa; no borra.
