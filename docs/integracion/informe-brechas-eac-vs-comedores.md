# Informe de brechas — solución existente (EAC) frente a Plataforma (punto 8) y Comedores

> **Consultar cuando:** se decide cómo integrar Comedores y la plataforma mínima en la solución existente.
> **Certeza:** los hechos salen de la lectura del repositorio (ruta indicada por el usuario, solo lectura). Las comparaciones con el punto 8 son **inferidas**; las opciones, **propuestas**. Nada de lo siguiente está aprobado.
> **Verificado el 2026-10-30 (ver sección 8):** estado de Git, compilación, ejecución de las suites de unidad, integración y contrato, e inventario de pantallas y pruebas del frontend. Las cifras corregidas de las secciones 1 y 3 salen de esa verificación.
> **No verificado:** contenido de las pruebas, cuerpo de las migraciones y de `spec.md`/`data-model.md`, ejecución de las pruebas del frontend (Vitest y Playwright).

## 1. Qué es la solución hoy (hechos)

| Tema | Hallazgo |
|---|---|
| Producto | "Enterprise Access Control Platform" (EAC): control de acceso físico para minería e industria. Etapa 1 declarada **cerrada** (T001–T242 y validación post-baseline) |
| Stack | .NET 10, C# 13, ASP.NET Core 10 Web API (controladores), EF Core 10, SQL Server, FluentValidation 12, NodaTime, JWT Bearer, OpenAPI nativo, ProblemDetails. `TreatWarningsAsErrors` activo. Ratificado en la constitución |
| Estructura | Un monolito en capas: `Api`, `Application`, `Domain`, `Infrastructure`, más pruebas de unidad (155), integración (593, SQL Server 2022 por Testcontainers) y contrato (252 con snapshot OpenAPI; antes figuraba 246). Solución `backend/EnterpriseAccessControl.slnx` con 7 proyectos. Frontend React + TypeScript + Vite con pruebas Vitest y E2E Playwright (inventario en 8.3) |
| Organización del código | `Application` por carpetas de funcionalidad (AreaAccess, Auth, Companies, Credentials, People, Permissions…). Un único `AppDbContext` expuesto a todos los casos de uso mediante `IAppDbContext` |
| Proceso | Spec Kit (`.specify/`, skills `speckit-*`), constitución v1.1.1 (7 principios), especificación con 81 RF, 22 entidades, 13 migraciones EF (12 de esquema y 1 de datos semilla de maestros, `SeedMaestrosPeru`; antes figuraban 12) y 6 triggers SQL de no solapamiento (verificado) |
| Ausentes | Integración continua (no hay `.github/workflows` ni `azure-pipelines.yml`, verificado); adaptadores de terminales. `CLAUDE.md` y `docs/README.md` ya existen (añadidos el 2026-10-30) |
| Documentos propios | `docs/` ya tiene `auditorias`, `final` y `functional-validation`. Las carpetas nuevas (`ewop`, `multitenancy`, `modulos`, `decisiones`, `integracion`) **no colisionan** |

## 2. Cómo se corresponde el modelo existente con D8-03

| D8-03 | Existente | Valoración |
|---|---|---|
| Tenant (grupo empresarial) | No existe. Ninguna entidad lleva `TenantId` | Brecha |
| Empresa mandante | `Compania` de tipo principal/mandante | Equivale |
| Contratista con varias relaciones | `Compania` contratista + `RelacionContratistaPrincipal` (N:N con vigencia y no solapamiento) | Equivale |
| Sede | No existe. `AreaAcceso` es árbol físico por principal; `UnidadOrganizativa` es árbol organizativo | Brecha |
| Área o punto operativo | `AreaAcceso` (acceso físico) | Parcial: ¿un comedor es un `AreaAcceso`? Sin decidir |
| Persona única | `Persona`, sin dueño de tenant | Parcial |
| Vínculo (uno activo, con historial) | `AsignacionPersonaCompania` (pertenencia con vigencia, trigger de no solapamiento, cascada de revocación) | Equivale en lo esencial |
| Identidad operativa (una por vínculo) | `AsignacionCredencial` por principal, dentro de `ContextoOperativoPersonaPrincipal` | Difiere: depende del contexto por principal, no del vínculo |
| Usuario de plataforma | `Usuario` + `AsignacionRolAdministrativo` | Equivale |
| Suscripción | No existe | Brecha |
| Categoría del trabajador | Probable `TipoPersona`/`AsignacionTipoPersona` (a confirmar en `data-model.md`) | Por verificar |
| Grupo, centro de costos, cargo, código personal | No existen | Brecha (Q-16) |

## 3. Brechas frente a las reglas de la plataforma (D8-01 a D8-05)

| # | Regla del punto 8 | Estado actual | Efecto en Comedores |
|---|---|---|---|
| B-01 | Tenant derivado de la identidad autenticada, falla cerrado (D8-05) | Aislamiento solo por **alcance de compañía** a partir de claims del JWT | Alto: sin tenant no hay licencia ni aislamiento entre grupos |
| B-02 | Licencia, permiso y alcance evaluados en un único punto y revalidados por operación | El alcance viaja en el token (60 min): **cambiar asignaciones no surte efecto hasta nuevo login**. Hay una política de plataforma más verificaciones por caso de uso | Alto, y se agrava con Blazor Server (circuitos largos, D-FE-01) |
| B-03 | Roles base más personalizados, permisos por módulo | Catálogo **cerrado** de 2 roles (GLOBAL y COMPANY administrator); sin catálogo de permisos | Alto: Comedores necesita permisos propios (ver 06-integracion) |
| B-04 | Entitlement por módulo y límites; gracia y cuarentena | No existe | Medio al inicio (un cliente), alto al operar varios |
| B-05 | Configuración con herencia área > sede > mandante > tenant | Solo zona horaria por compañía con respaldo global | Medio: precio, horario y consumo máximo del servicio |
| B-06 | Auditoría atómica de operaciones sensibles | El interceptor solo estampa `CreatedAt/UpdatedAt/By`. La auditoría agregada quedó **diferida** (RF-067 a RF-069) | Alto: cargas masivas, restricciones y cambios de precio deben auditarse |
| B-07 | Cada módulo es dueño de sus datos; nadie lee tablas ajenas | `IAppDbContext` expone todo; no hay frontera entre módulos | Alto si Comedores entra en el mismo contexto |
| B-08 | Contexto del momento guardado en cada registro | Históricos con vigencia sí; no hay registro de eventos | Se resuelve dentro del módulo |
| B-09 | Terminales por adaptador y contrato; ningún evento se descarta; cuarentena | No existe ingestión de eventos ni identidad de dispositivo | Alto: es el corazón del consumo |
| B-10 | Contrato de ciclo de vida de datos (bloquear, exportar, eliminar con evidencia) | No existe. La decisión D9 de Etapa 1 fijó "sin retención ni purga" | Medio, con validación legal pendiente |
| B-11 | Frontend Blazor Server en toda la solución, consumiendo la API por HTTP | SPA React completa de Etapa 1 con autenticación JWT | Hay que reconstruir las pantallas de Etapa 1 (13 rutas en 10 carpetas de `features`, ver 8.3), clasificar los 72 endpoints (verificado: 72 atributos `[Http*]` en 10 controladores) y retirar React y lo no reutilizable a una carpeta fuera de la solución |

## 4. Qué se puede reutilizar (inferido, a validar en revisión de código)

`EntidadBase` (UUID v7 y campos de auditoría), `IRelojEmpresarial` y zona horaria IANA, patrón de vigencia (`FechaHoraInicio`/`Fin`, contención, triggers de no solapamiento), `ErrorNegocioException` y `CodigosError`, ProblemDetails, validación con FluentValidation, `EjecutarEnTransaccionAsync`, el patrón de pruebas de contrato con snapshot OpenAPI, la estructura de Spec Kit, y los catálogos maestros. El `EvaluadorDeAcceso` (15 pasos) es de **acceso físico**: no debe reutilizarse para validar consumos; Comedores necesita su propio evaluador.

## 5. Decisiones

**Respondidas por el usuario el 2026-10-30:**

- **I-1:** toda la solución pasa a Blazor Server y la SPA React se retira; no habrá convivencia permanente (ver [D-FE-01](../decisiones/D-FE-01-frontend-blazor-server.md)). Esto elimina el doble inicio de sesión, pero no el problema de `HttpContext` en circuitos, que es propio de Blazor Server.
- **I-2:** las reglas de tenant y licenciamiento aplican completas a Comedores, lo que obliga a introducir el tenant ahora (se asume tenant por defecto para el primer cliente; detalle por diseñar).
- **Alcance:** sin reportes, horario de interfaces, migraciones ni consumos manuales ([D-ALC-01](../decisiones/D-ALC-01-alcance-etapa-comedores.md)).

**Siguen abiertas (I-3 a I-6), el esquema de autenticación de la UI y el destino de los endpoints HTTP (Q-26, Q-28):**

| # | Decisión | Opciones | Mi recomendación (a validar) |
|---|---|---|---|
| I-3 | Frontera de Comedores | (A) carpetas dentro de los proyectos actuales; (B) proyectos propios (Domain, Application, Infrastructure) con esquema propio y acceso al núcleo solo por interfaces; (C) extraer primero la plataforma | (B): respeta D8-05 sin refactorizar Etapa 1 |
| I-4 | Qué es un comedor en el modelo | punto operativo propio, o `AreaAcceso` | Punto operativo propio (inferido): no mezcla acceso físico con consumo |
| I-5 | Cómo se llevan las reglas nuevas | Enmienda de la constitución (MINOR 1.2.0) más especificaciones nuevas por Spec Kit | Sí; ver 6 |
| I-6 | Roles | Sustituir el catálogo de 2 roles por permisos y roles configurables, o extenderlo por módulo | Decidir dentro de la especificación de plataforma mínima |

## 6. Secuencia propuesta dentro de Spec Kit

El orden completo, con puntos de control y el momento de abrir VS Code, está en [orden-de-ejecucion.md](orden-de-ejecucion.md); los prompts, en [prompts-de-arranque.md](prompts-de-arranque.md) y [prompts-migracion-pantallas.md](prompts-migracion-pantallas.md).

## 7. Cambios en la carpeta

- Hecho el 2026-10-30: añadir `docs/` (carpetas nuevas) y un `CLAUDE.md` en la raíz. **No existe uno, así que no hay nada que fusionar**.
- Actualizar en esos documentos lo que hoy figura como "stack sin confirmar" con los hechos de este informe (P0-1, P0-2, P0-3).
- No tocar `backend/`, `frontend/`, `.specify/` ni `specs/`. Los prompts de arranque están en [prompts-de-arranque.md](prompts-de-arranque.md); las decisiones I-3 a I-6, Q-26 y Q-28 se cierran dentro de ese flujo, con confirmación del usuario.

## 8. Verificación del estado real (2026-10-30)

Ejecutada sin modificar código, sin instalar dependencias globales y sin borrar nada. SDK `dotnet` 10.0.403; Docker Desktop 29.9.2.

### 8.1 Git

| Comando | Resultado |
|---|---|
| `git status` | Rama `main`, al día con `origin/main`, árbol limpio |
| `git log -10` | Un solo commit: `efc106f` "chore: linea base inicial (EAC Etapa 1 y documentacion de contexto)", 2026-10-30 |
| `git branch -a`, `git tag` | Solo `main` y `origin/main`. **No hay etiquetas**: hay que crear la etiqueta previa que exige CLAUDE.md antes de mover o retirar nada |

### 8.2 Compilación y pruebas

| Paso | Resultado |
|---|---|
| `dotnet build backend/EnterpriseAccessControl.slnx` | **Falla en la restauración**: 8 errores NU1902/NU1903. `System.Security.Cryptography.Pkcs` 9.0.11 tiene vulnerabilidades conocidas (moderada GHSA-2fmv-rv2c-59x7, alta GHSA-846f-f764-76mx), y `TreatWarningsAsErrors` las convierte en errores. Afecta a Infrastructure, Api, IntegrationTests y ContractTests. Es dependencia transitiva; el origen probable es `Microsoft.EntityFrameworkCore.SqlServer` 10.0.12 → `Microsoft.Data.SqlClient` (inferido, sin confirmar) |
| Igual, con `-p:WarningsNotAsErrors=NU1902%3BNU1903` (solo diagnóstico, en la línea de comandos) | Compilan los 7 proyectos: 0 errores, 16 advertencias (todas NU1902/NU1903). El código compila; lo que falla es la auditoría de paquetes |
| Unidad (`--no-build`) | 155/155 pasan |
| Integración (`--no-build`) | 593/593 pasan (1 min 5 s) |
| Contrato (`--no-build`) | 252/252 pasan |

**Pendiente de decisión (no aplicado):** cómo corregir la compilación. Opciones: fijar una versión corregida de `System.Security.Cryptography.Pkcs` o actualizar EF Core/SqlClient. No se recomienda suprimir el aviso. Hasta corregirlo, ninguna rebanada nueva compila con la configuración del repositorio.

### 8.3 Pantallas del frontend React (`frontend/src/features`)

Rutas declaradas en `src/app/App.tsx`. La ruta `/` (Dashboard) es un marcador `PantallaPendiente`, no una pantalla real.

| Ruta | Pantalla (carpeta) | Componentes incluidos | Pruebas Vitest (`tests/unit`) | E2E Playwright (`tests/e2e`) |
|---|---|---|---|---|
| `/login` | `LoginPage` (auth) | — | `LoginPage` (6) | `spa-login` (indirecto); todas las E2E con UI inician sesión |
| `/cambiar-password` | `CambiarPasswordPage` (auth) | — | — | — |
| `/usuarios` | `UsuariosPage` (users) | `CrearUsuarioWizard`, `UsuarioDetalle`, `AsignarRolDialogo`, `UsuarioFormulario` | `UsuariosPage` (17), `UsuarioDetalle` (11) | `administracion-usuarios` (3: UX-17 a UX-22, búsqueda, alcance COMPANY_ADMINISTRATOR) |
| `/companias` | `CompaniasPage` (companies) | `CompaniaFormulario`, `RelacionesDialogo` | `CompaniasPage` (11) | `spa-login` (1: carga de datos protegidos) |
| `/unidades-organizativas` | `UnidadesOrganizativasPage` (org-units) | — | `UnidadesOrganizativasPage` (10) | — |
| `/areas-acceso` | `AreasAccesoPage` (area-access) | `TiposPersonaPorArea` | `AreasAccesoPage` (15), `TiposPersonaPorArea` (9) | — |
| `/permisos` | `PermisosPage` (permissions) | `PermisoFormulario`, `SelectorSemanal` | `PermisosPage` (24) | — |
| `/evaluacion-acceso` | `EvaluacionAccesoPage` (access-evaluation) | — | `EvaluacionAccesoPage` (8) | — |
| `/maestros/:catalogo` | `MaestrosPage` (masters) | `MaestroFormulario` | `MaestrosPage` (10) | — |
| `/personas` | `PersonasPage` (people) | `PersonaFormulario` | `PersonasPage` (10) | — |
| `/personas/:id/historial` | `PersonaHistorialPage` (people/history) | `PertenenciaContextoWizard`, `RenovarPertenenciaDialogo`, `AsignacionUnidadOrganizativa`, `PerfilesPersona`, `CredencialesPersona` | `PersonaHistorialPage` (10), `PertenenciaContextoWizard` (4), `AsignacionUnidadOrganizativa` (9), `PerfilesPersona` (4), `CredencialesPersona` (9) | `historia5-casos-a-b` (3: Caso A, Caso B, CS-021) |

Pruebas transversales: `RutaProtegida` (4), `Tree` (10), `Tree.a11y` (15), `fechas` (3). En total hay 20 archivos Vitest con unos 199 casos, contados por texto (no ejecutados). Hay 5 especificaciones E2E con 9 pruebas; `cs009-quickstart` y `multi-principal-quickstart` recorren el flujo por API, sin navegar pantallas. **Sin E2E:** cambio de contraseña, unidades organizativas, áreas de acceso, permisos, evaluación de acceso, maestros y personas (lista). Ni Vitest ni Playwright se ejecutaron en esta verificación.
