# Inventario de endpoints y pruebas — destino tras D-FE-01

> **Estado:** **propuesta aprobada con reservas** (usuario, 2026-10-31). Resueltas: DUDA-2 y DUDA-4. **Diferidas, sin decidir:** DUDA-1 (especificación 002), DUDA-3 y DUDA-6 (especificación 003) y DUDA-5 (especificación 005). Ver §5. Las clasificaciones que dependen de una duda diferida **no están aprobadas**. Nada se ha movido ni retirado.
> **Fecha:** 2026-10-31
> **Origen:** [D-FE-01](../decisiones/D-FE-01-frontend-blazor-server.md), sección "Destino de los endpoints y las pruebas" (cierra Q-28). Este documento es el "inventario aprobado" que esa decisión exige antes de tocar nada.
> **Base:** `main` en `ebe770b`. No existe todavía ninguna etiqueta de Git (`git tag` vacío).

## 1. Cómo se hizo y qué grado de certeza tiene

| Dato | Cómo se obtuvo | Certeza |
|---|---|---|
| 72 endpoints, ruta, método, controlador, política | Lectura de los 10 controladores de `backend/src/EnterpriseAccessControl.Api/Controllers` | Verificado |
| Servicio de aplicación invocado | Lectura del cuerpo de cada acción | Verificado |
| Origen del alcance | `AlcanceCompaniaAccessor` lee el rol del **claim del token**; `CompaniaScopeAuthorizationHandler` solo exige "alcance vigente"; `GET /api/auth/sesion` sí lo resuelve desde la BD (`AutenticacionService.ResolverAlcanceAsync`) | Verificado |
| Consumo actual por la SPA | `grep` de `/api/` en `frontend/src/features/*/api.ts` | Verificado |
| Pruebas que cubren cada endpoint | `grep` de rutas literales en cada archivo de prueba y en sus ayudantes (`EscenarioUs5`, `EscenarioPermisos`, `CascadaSoporte`, `CredencialesSoporte`, `AreasAccesoSoporte`, `UnidadesOrganizativasSoporte`) | **Inferido**: se atribuye por ruta; cuando una ruta admite varios métodos, el método se dedujo del ayudante o del nombre de la prueba. No se midió cobertura ejecutando |
| Clasificación propuesta | Criterio descrito en §2 | **Propuesta**, a aprobar |

### 1.1 Recuento de endpoints (verificado el 2026-10-31)

Atributos `[Http*]` por controlador (`grep -n "\[Http"` en `backend/src/EnterpriseAccessControl.Api/Controllers`):

| Controlador | Líneas con `[Http*]` | Subtotal | Filas |
|---|---|---|---|
| `AuthController` | 15, 29, 43 | 3 | #1-3 |
| `CompaniasController` | 20, 34, 40, 51, 66, 74, 91 | 7 | #4-10 |
| `UnidadesOrganizativasController` | 23, 32, 40, 46, 58, 67 | 6 | #11-16 |
| `AreasAccesoController` | 23, 32, 40, 46, 58, 68, 80, 86 | 8 | #17-24 |
| `PermisosController` | 23, 39, 45, 58 | 4 | #25-28 |
| `EvaluacionAccesoController` | 25 | 1 | #29 |
| `MaestrosController` | 57, 64, 71, 82, 89, 96, 106, 113, 120, 130, 137, 144, 154, 161, 168 | 15 | #30-44 |
| `PersonasController` | 27, 41, 47, 58, 69, 76, 90, 104, 119, 126, 140, 148, 165, 172, 186 | 15 | #45-59 |
| `CredencialesController` | 21, 30, 46, 56 | 4 | #60-63 |
| `UsuariosController` | 34, 47, 53, 66, 75, 86, 94, 110, 134 | 9 | #64-72 |
| **Total** | | **72** | |

### 1.2 Comprobación por muestreo (2026-10-31)

Se contrastaron tres filas de controladores distintos con el código (ruta, servicio y método HTTP de cada llamada en las pruebas citadas):

| Fila | Ruta | Servicio | Pruebas | Resultado |
|---|---|---|---|---|
| #9 `POST /api/companias/{contratistaId}/relaciones-principales` | Coincide (`CompaniasController.cs:74`) | Coincide: `relaciones.CrearAsync` | C: `CompaniasContractTests:53` (`post`); I: `RelacionContratistaPrincipalTests:42-43` y `AuditoriaTests:138-139` usan `PostAsJsonAsync` | **Coincide** |
| #24 `PUT /api/areas-acceso/{id}/tipos-persona` | Coincide (`AreasAccesoController.cs:86`) | Coincide: `areas.ReemplazarTiposPersonaAsync` | Coinciden las citadas. Además `EscenarioPermisos.MontarAsync` (línea 92) lo invoca, así que lo ejercitan **todas** las pruebas que montan ese escenario | **Coincide, pero la cita se quedaba corta**: corregida |
| #67 `PUT /api/usuarios/{id}` | Coincide (`UsuariosController.cs:66`) | Coincide: `usuarios.ActualizarAsync` | C: `UsersContractTests:66` (`put`) e I: `RolesAdministrativosTests:85` (`PutAsJsonAsync`) coinciden. **`AislamientoRecursosTests` no**: sobre `/api/usuarios/{id}` solo hace `GetAsync` (líneas 99-105) | **No coincide**: se había atribuido por ruta, sin mirar el método. Corregida |

Como el error de #67 venía del método de atribución, se revisaron también los pares método + ruta de `Auth/AislamientoRecursosTests`, que se cita en muchas filas. Correcciones aplicadas: se quita de #50, #55 y #67 y se añade a #15, #21, #49, #54, #56 y #61. Las demás atribuciones siguen siendo **inferidas** (§1).

Recuento de pruebas (atributos `[Fact]`/`[Theory]`; las `Theory` se expanden al ejecutar, por eso [linea-base.md](linea-base.md) da 155 / 593 / 252 casos):

| Proyecto | Archivos con pruebas | Métodos |
|---|---|---|
| Contrato | 15 | 140 |
| Integración | 70 | 509 |
| Unidad | 11 | 110 |

## 2. Criterio de clasificación

**Hallazgo que condiciona todo el inventario:** los 72 endpoints dependen de tres mecanismos transversales que hoy no cumplen las reglas de la plataforma:

- **T — Tenant** (B-01): no existe; el aislamiento es solo por alcance de compañía. Afecta a todo endpoint que lea o escriba datos.
- **A — Alcance por solicitud** (B-02): el rol y las compañías viajan en el token durante 60 minutos. D-FE-01 exige resolverlos por solicitud desde la BD para que un cambio llegue a un circuito Blazor abierto.
- **P — Permiso por módulo** (B-03): hoy hay una sola política (`CompaniaScope`) y un catálogo cerrado de 2 roles. Comedores necesita permisos propios.

Por eso **casi ningún endpoint puede "reutilizarse tal cual"** en sentido estricto. Pero la mayor parte de la adaptación vive en piezas comunes (accessor de alcance, política, filtro global por tenant, emisión del token), **no en cada controlador**. Para distinguirlo se añade la columna "¿Cambia el controlador?":

| Clasificación | Significado |
|---|---|
| **Reutilizar** | Ni el controlador ni su servicio cambian; tampoco le afectan T/A/P de forma visible |
| **Adaptar (T/A/P)** | Le afectan los mecanismos indicados. "Controlador: no" = el controlador no cambia y el endpoint conserva ruta y contrato. **No quiere decir que el cambio sea pequeño** (ver la nota de abajo) |
| **Retirar** | Se mueve a la carpeta fuera de la solución (Q-30) |
| **DUDA-n** | Caso dudoso: se propone una opción pero **decide el usuario** (ver §5) |

> **Nota: "controlador: no" no es un cambio pequeño.** Para filtrar por tenant, cada entidad con datos de cliente necesita un `TenantId` (o una relación que lo determine sin ambigüedad). Eso supone cambios en el modelo de dominio y en la configuración de EF Core, **migraciones nuevas** sobre tablas que ya tienen datos (columna, rellenado de las filas existentes, índices y claves únicas que pasan a incluir el tenant, revisión de los 6 triggers de no solapamiento), un filtro global de consultas y la semilla de los fixtures de prueba. Que la ruta y el controlador no cambien no reduce ese trabajo: la "adaptación" de los 71 endpoints es, en conjunto, un cambio de esquema y de modelo. Se dimensiona en la especificación correspondiente, no aquí.

Consumidor previsto: **UI Blazor** cuando la SPA lo usa hoy y la pantalla se reconstruye (D-FE-01); **Ninguno hoy** cuando la SPA no lo llama. Ningún endpoint actual es de terminales: los de ingestión serán nuevos y con su propio contrato (D-FE-01, B-09).

Abreviaturas de pruebas: **C** = contrato (`EnterpriseAccessControl.ContractTests`), **I** = integración (`EnterpriseAccessControl.IntegrationTests`). Se omite el sufijo `Tests`. No se repiten en cada fila tres pruebas que cubren **todos** los endpoints: `C/OpenApiSnapshot` y `C/AuditFieldsNotExposed` (documento OpenAPI frente a `specs/001-…/contracts`) e `I/Security/EndurecimientoSeguridad` (barrido por reflexión: anónimo, 401 sin token, 403 sin alcance).

## 3. Inventario de endpoints (72)

### 3.1 Auth — `AuthController` → `AutenticacionService` (3)

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 1 | `POST /api/auth/login` (anónimo) | `LoginAsync` | C: Auth · I: Auth/Login, Auth/PasswordPolicy | UI Blazor (host del servidor, Q-26) | **Adaptar (T, A)** · controlador: no · **DUDA-1, diferida (002)** | El tenant debe salir de la identidad del usuario y el token deja de ser la fuente del alcance. Cómo obtiene y renueva el token el host Blazor está por decidir (Q-26) |
| 2 | `POST /api/auth/cambiar-password` | `CambiarPasswordAsync` | C: Auth · I: Auth/PasswordPolicy | UI Blazor | **Reutilizar** | Opera sobre la cuenta propia por `sub`; no lee datos de compañía. El filtro por tenant sobre `Usuario`, si se añade, es transparente |
| 3 | `GET /api/auth/sesion` | `ObtenerSesionAsync` | C: Auth · I: Auth/Login | UI Blazor | **Adaptar (T, P)** · controlador: no | Ya resuelve el alcance desde la BD. Debe añadir tenant, licencias del módulo y permisos para que la UI filtre la navegación (la UI no es la barrera) |

### 3.2 Compañías — `CompaniasController` (7)

Servicios: `CompaniaService` (#4-7), `RelacionContratistaPrincipalService` (#8-10). Pantalla: Compañías y `RelacionesDialogo` (necesaria para Comedores).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 4 | `GET /api/companias` | `ListarAsync` | C: Companias · I: Companies/Companias, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Listado filtrado por alcance: además debe quedar dentro del tenant y con alcance vigente por solicitud |
| 5 | `GET /api/companias/{id}` | `ObtenerAsync` | C: Companias · I: Companies/Companias, Auth/AislamientoRecursos | Ninguno hoy | **Adaptar (T, A, P)** · controlador: no · DUDA-2 resuelta: se conserva | La SPA no lo usa. Lo sostienen las pruebas de aislamiento (404 fuera de alcance) |
| 6 | `POST /api/companias` | `CrearAsync` | C: Companias · I: Companies/Companias, Auditing/Auditoria | UI Blazor | **Adaptar (T, A, P)** · controlador: no | La compañía nace dentro del tenant del usuario. Alta de mandante/contratista: permiso administrativo explícito |
| 7 | `PUT /api/companias/{id}` | `ActualizarAsync` | C: Companias · I: Companies/Companias, Companies/CambioTipoCompania, Auditing/Auditoria, Permissions/PermisosHistoricosVigenciaDiaria | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #6 |
| 8 | `GET /api/companias/{contratistaId}/relaciones-principales` | `ListarAsync` | C: Companias · I: Companies/RelacionContratistaPrincipal | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Relación contratista↔mandante: ambas compañías deben ser del mismo tenant |
| 9 | `POST /api/companias/{contratistaId}/relaciones-principales` | `CrearAsync` | C: Companias · I: Companies/RelacionContratistaPrincipal, Auditing/Auditoria | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #8; rechazar relaciones entre tenants |
| 10 | `POST /api/companias/{contratistaId}/relaciones-principales/{id}/finalizar` | `FinalizarAsync` | C: Companias · I: Companies/RelacionContratistaPrincipal, Auditing/Auditoria | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #8 |

### 3.3 Unidades organizativas — `UnidadesOrganizativasController` → `UnidadOrganizativaService` (6)

Pantalla: Unidades organizativas (no necesaria para Comedores, pero se reconstruye por D-FE-01).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 11 | `GET /api/unidades-organizativas` | `ListarAsync` | C: UnidadesOrganizativas · I: OrgUnits/UnidadesOrganizativas, OrgUnits/AislamientoMultiPrincipal | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Filtro por mandante dentro del alcance: añadir tenant y alcance por solicitud |
| 12 | `GET /api/unidades-organizativas/arbol` | `ObtenerArbolAsync` | C: UnidadesOrganizativas · I: OrgUnits/UnidadesOrganizativas, OrgUnits/UnidadRaizContratistaRechazada, OrgUnits/AislamientoMultiPrincipal, OrgUnits/MoverEntrePrincipalesRechazado | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #11 |
| 13 | `GET /api/unidades-organizativas/{id}` | `ObtenerAsync` | C: UnidadesOrganizativas · I: OrgUnits/UnidadesOrganizativas, OrgUnits/MoverEntrePrincipalesRechazado, Auth/AislamientoRecursos | Ninguno hoy | **Adaptar (T, A, P)** · controlador: no · DUDA-2 resuelta: se conserva | La SPA no lo usa |
| 14 | `POST /api/unidades-organizativas` | `CrearAsync` | C: UnidadesOrganizativas · I: OrgUnits/UnidadesOrganizativas, OrgUnits/UnidadRaizContratistaRechazada | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #11 |
| 15 | `PUT /api/unidades-organizativas/{id}` | `ActualizarAsync` | C: UnidadesOrganizativas · I: OrgUnits/UnidadesOrganizativas, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #11 |
| 16 | `POST /api/unidades-organizativas/{id}/mover` | `MoverAsync` | C: UnidadesOrganizativas · I: OrgUnits/UnidadesOrganizativas, OrgUnits/MoverEntrePrincipalesRechazado | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #11 |

### 3.4 Áreas de acceso — `AreasAccesoController` → `AreaAccesoService` (8)

Pantalla: Áreas de acceso y `TiposPersonaPorArea` (acceso físico; no necesaria para Comedores, pero se reconstruye).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 17 | `GET /api/areas-acceso` | `ListarAsync` | C: AreasAcceso · I: AreaAccess/AreasAcceso, AreaAccess/AislamientoAreasMultiPrincipal, AreaAccess/AreaRaizContratistaRechazada | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Datos por mandante dentro del alcance |
| 18 | `GET /api/areas-acceso/arbol` | `ObtenerArbolAsync` | C: AreasAcceso · I: AreaAccess/AreasAcceso, AreaAccess/AislamientoAreasMultiPrincipal, AreaAccess/MoverAreaEntrePrincipalesRechazado, AreaAccess/AreaRaizContratistaRechazada | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #17 |
| 19 | `GET /api/areas-acceso/{id}` | `ObtenerAsync` | C: AreasAcceso · I: AreaAccess/AreasAcceso, AreaAccess/MoverAreaEntrePrincipalesRechazado, Auth/AislamientoRecursos | Ninguno hoy | **Adaptar (T, A, P)** · controlador: no · DUDA-2 resuelta: se conserva | La SPA no lo usa |
| 20 | `POST /api/areas-acceso` | `CrearAsync` | C: AreasAcceso · I: AreaAccess/* (los 5 archivos), Permissions/* vía `EscenarioPermisos.CrearAreaAsync` | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #17 |
| 21 | `PUT /api/areas-acceso/{id}` | `ActualizarAsync` | C: AreasAcceso · I: AreaAccess/AreasAcceso, Auth/AislamientoRecursos, Permissions/EvaluacionAcceso (vía `DesactivarAreaAsync`) | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #17 |
| 22 | `POST /api/areas-acceso/{id}/mover` | `MoverAsync` | C: AreasAcceso · I: AreaAccess/AreasAcceso, AreaAccess/MoverAreaEntrePrincipalesRechazado | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #17 |
| 23 | `GET /api/areas-acceso/{id}/tipos-persona` | `ObtenerTiposPersonaAsync` | C: AreaAccesoTipoPersona · I: AreaAccess/AreaAccesoTipoPersona | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #17; depende de DUDA-3 (catálogo de tipos de persona), diferida a la especificación 003 |
| 24 | `PUT /api/areas-acceso/{id}/tipos-persona` | `ReemplazarTiposPersonaAsync` | C: AreaAccesoTipoPersona · I: AreaAccess/AreaAccesoTipoPersona; todas las pruebas que montan `EscenarioPermisos` (`MontarAsync` llama a `AutorizarPerfilEnAreaAsync`), y de forma explícita Permissions/EvaluacionAcceso y EvaluacionAccesoContratista | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #23 |

### 3.5 Permisos de acceso — `PermisosController` → `PermisoAccesoService` (4)

Pantalla: Permisos (acceso físico; no necesaria para Comedores, se reconstruye).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 25 | `GET /api/permisos` | `ListarAsync` | C: Permisos · I: Permissions/Permisos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Datos de persona y mandante dentro del alcance |
| 26 | `GET /api/permisos/{id}` | `ObtenerAsync` | C: Permisos · I: Permissions/Permisos | Ninguno hoy | **Adaptar (T, A, P)** · controlador: no · DUDA-2 resuelta: se conserva | La SPA no lo usa |
| 27 | `POST /api/permisos` | `CrearAsync` | C: Permisos · I: Permissions/Permisos, ContencionPermisoFechaCivil, ContencionPermisosPersona, VigenciaDiariaPermisos, People/Rf082RenovacionYCascada, Auditing/Auditoria, Auditing/Trazabilidad; además todo `Permissions/*` vía `EscenarioPermisos.CrearPermisoAsync` | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #25 |
| 28 | `PUT /api/permisos/{id}` | `ActualizarAsync` | C: Permisos · I: Permissions/Permisos, ContencionPermisosPersona, PermisosHistoricosVigenciaDiaria, RegistrosAnterioresRf082, Auditing/Auditoria, Auditing/Trazabilidad | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #25 |

### 3.6 Evaluación de acceso — `EvaluacionAccesoController` → `EvaluacionAccesoService` (1)

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 29 | `POST /api/evaluacion-acceso` | `EvaluarAsync` (usa `EvaluadorDeAcceso`, 15 pasos) | C: EvaluacionAcceso · I: Permissions/EvaluacionAcceso, EvaluacionAccesoContratista, EvaluacionAccesoPerformance, GateCredencial, PrecedenciaPermisos, RevalidacionDinamica, CompaniaInactivaYZonaHoraria, VigenciaDiariaPermisos, PermisosHistoricosVigenciaDiaria | UI Blazor (pantalla de consulta) | **Adaptar (T, A, P)** · controlador: no · DUDA-4 resuelta | Es acceso físico. Confirmado por el usuario (2026-10-31): queda **solo como pantalla de Etapa 1** y **no se reutiliza en Comedores** ni se expone a terminales |

### 3.7 Datos maestros — `MaestrosController` → `MasterDataService<TEntity>` (15)

Los 15 endpoints delegan en el mismo servicio genérico. Pantalla: Maestros (necesaria para Comedores: catálogos de personas). Pruebas comunes a los 15: **C: Maestros** (estática, sobre el documento OpenAPI) · **I: Masters/Maestros** (parametrizada con `MemberData(Catalogos)`).

| # | Método y ruta | Servicio | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|
| 30 | `GET /api/maestros/tipos-documento` | `ListarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Hoy son catálogos globales sin compañía; con tenant hay que decidir si son de plataforma o de cada tenant |
| 31 | `POST /api/maestros/tipos-documento` | `CrearAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Si son de plataforma, editarlos no puede quedar en manos del administrador de un tenant |
| 32 | `PUT /api/maestros/tipos-documento/{id}` | `ActualizarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 33 | `GET /api/maestros/tipos-sangre` | `ListarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #30 |
| 34 | `POST /api/maestros/tipos-sangre` | `CrearAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 35 | `PUT /api/maestros/tipos-sangre/{id}` | `ActualizarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 36 | `GET /api/maestros/generos` | `ListarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #30 |
| 37 | `POST /api/maestros/generos` | `CrearAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 38 | `PUT /api/maestros/generos/{id}` | `ActualizarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 39 | `GET /api/maestros/tipos-persona` | `ListarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #30; además Comedores puede usar el tipo de persona como categoría del trabajador (Q-16) |
| 40 | `POST /api/maestros/tipos-persona` | `CrearAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 41 | `PUT /api/maestros/tipos-persona/{id}` | `ActualizarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 42 | `GET /api/maestros/tipos-credencial` | `ListarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #30 |
| 43 | `POST /api/maestros/tipos-credencial` | `CrearAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |
| 44 | `PUT /api/maestros/tipos-credencial/{id}` | `ActualizarAsync` | UI Blazor | **Adaptar (A, P)** · **DUDA-3, diferida (003)** | Igual que #31 |

Controlador: no cambia en ninguno de los 15 si los catálogos siguen siendo globales; si pasan a ser de cada tenant, cambia el servicio genérico, no el controlador.

### 3.8 Personas — `PersonasController` (15)

Servicios: `PersonaService` (#45-48), `HistorialPersonaService` (#49-52), `ContextoOperativoService` (#53-54), `AsignacionUnidadOrganizativaService` (#55-56), `EstadoEfectivoService` (#57-59). Pantallas: Personas, Historial de persona (necesarias para Comedores), Perfiles y Unidad organizativa (por confirmar, Q-16).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 45 | `GET /api/personas` | `PersonaService.BuscarAsync` | C: Personas · I: People/PersonasBusqueda, People/PersonasPerformance | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Búsqueda limitada al alcance (RF-035): añadir tenant y alcance por solicitud |
| 46 | `GET /api/personas/{id}` | `PersonaService.ObtenerAsync` | C: Personas · I: People/PersonasBusqueda, Auth/AislamientoRecursos | Ninguno hoy | **Adaptar (T, A, P)** · controlador: no · DUDA-2 resuelta: se conserva | La SPA no lo usa. Clave en el aislamiento (404 en vez de 403) |
| 47 | `POST /api/personas` | `PersonaService.CrearAsync` | C: Personas · I: People/Personas, Auditing/Auditoria | UI Blazor | **Adaptar (T, A, P)** · controlador: no | La persona pertenece al tenant. Unicidad de documento: ¿por tenant? (ver §5, relacionada con DUDA-3, diferida a 003) |
| 48 | `PUT /api/personas/{id}` | `PersonaService.ActualizarAsync` | C: Personas · I: People/Personas, People/PersonasBusqueda, Auditing/Auditoria, Auditing/Trazabilidad | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #47 |
| 49 | `GET /api/personas/{id}/historial-companias` | `HistorialPersonaService.ListarAsync` | C: HistorialPersona · I: People/HistorialPersona, Auth/AislamientoRecursos y casi todo `People/*` vía `EscenarioUs5.PertenenciaVigenteAsync` | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Vínculo persona↔compañía: base del derecho a consumir en Comedores |
| 50 | `POST /api/personas/{id}/historial-companias` | `HistorialPersonaService.CrearAsignacionAsync` | C: HistorialPersona · I: People/HistorialPersona, Auditing/Auditoria | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #49 |
| 51 | `POST /api/personas/{id}/historial-companias/{asignacionId}/finalizar` | `HistorialPersonaService.FinalizarAsync` | C: HistorialPersona · I: People/CeseExplicitoPertenencia, PreservacionHistorico, RevocacionFechaFutura, RevocacionMultiPrincipal, RevocacionPorCambioCompania, AislamientoPertenencias, EstadoEfectivo, PerfilesPersona, RenovacionPertenencia, Credentials/ListarCredenciales, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Dispara la cascada de revocación (RF-061). Comedores querrá reaccionar al cese: eso se resuelve por contrato entre módulos, no leyendo tablas (D8-05) |
| 52 | `POST /api/personas/{id}/historial-companias/{asignacionId}/renovar` | `HistorialPersonaService.RenovarAsync` | C: HistorialPersona, OpenApiSnapshot · I: People/RenovacionPertenencia | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #49 |
| 53 | `GET /api/personas/{id}/contextos-operativos` | `ContextoOperativoService.ListarAsync` | C: HistorialPersona · I: Auth/AislamientoRecursos y `People/*` vía `EscenarioUs5.ContextoAsync` | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Contexto persona↔mandante |
| 54 | `POST /api/personas/{id}/contextos-operativos` | `ContextoOperativoService.AbrirAsync` | C: HistorialPersona · I: People/ContextoOperativoCasoA, ContextoOperativoCasoB, ContextosMultiplesSimultaneos, CeseExplicitoPertenencia, ContencionTemporal, RenovacionPertenencia, FechasObligatorias, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #53 |
| 55 | `GET /api/personas/{id}/contextos-operativos/{contextoId}/unidad-organizativa` | `AsignacionUnidadOrganizativaService.ListarAsync` | C: HistorialPersona · I: People/AsignacionUnidadOrganizativa | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Pantalla por confirmar para Comedores (Q-16), pero se reconstruye igualmente |
| 56 | `POST /api/personas/{id}/contextos-operativos/{contextoId}/unidad-organizativa` | `AsignacionUnidadOrganizativaService.AsignarAsync` | C: HistorialPersona · I: People/AsignacionUnidadOrganizativa, ContencionTemporal, ContextosMultiplesSimultaneos, EstadoEfectivo, RenovacionPertenencia, Persistence/TriggersNoSolapamiento, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #55 |
| 57 | `GET /api/personas/{id}/perfiles` | `EstadoEfectivoService.ListarPerfilesAsync` | C: HistorialPersona · I: People/PerfilesPersona, Permissions/RegistrosAnterioresRf082 | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Perfil = tipo de persona; posible categoría del trabajador en Comedores (Q-16) |
| 58 | `POST /api/personas/{id}/perfiles` | `EstadoEfectivoService.AsignarPerfilAsync` | C: HistorialPersona · I: People/PerfilesPersona, ContencionPerfiles, ContencionTemporal, EstadoEfectivo | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #57 |
| 59 | `GET /api/personas/{id}/estado-efectivo?fechaHora=` | `EstadoEfectivoService.ObtenerAsync` | C: HistorialPersona · I: People/EstadoEfectivo, RevocacionFechaFutura, Auth/AislamientoRecursos | Ninguno hoy | **DUDA-5, diferida (005)** · sin clasificar (propuesta: adaptar) | La SPA no lo usa. Puede servir a Comedores para saber si la persona estaba vigente en un instante, o puede sobrar si Comedores obtiene ese dato por contrato interno |

### 3.9 Credenciales — `CredencialesController` → `CredencialService` (4)

Pantalla: `CredencialesPersona` (necesaria para Comedores: identidad operativa).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 60 | `GET /api/personas/{personaId}/credenciales` | `ListarAsync` | C: CredencialesApi, Credenciales · I: Credentials/ListarCredenciales, AsignarCredencial, EliminarCredencial, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Credencial ligada a persona y mandante |
| 61 | `POST /api/personas/{personaId}/credenciales` | `AsignarAsync` | C: CredencialesApi, Credenciales · I: Credentials/AsignarCredencial, DevolverCredencial, People/CredencialesAsignacion, ContencionTemporal, ContextosMultiplesSimultaneos, RenovacionPertenencia, Persistence/TriggersNoSolapamiento, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #60. Si Comedores identifica por tarjeta, la unicidad del código de credencial debe ser por tenant |
| 62 | `POST /api/personas/{personaId}/credenciales/{id}/devolver` | `DevolverAsync` | C: CredencialesApi, Credenciales · I: Credentials/DevolverCredencial, EliminarCredencial, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #60 |
| 63 | `DELETE /api/personas/{personaId}/credenciales/{id}` | `EliminarAsync` | C: CredencialesApi, Credenciales · I: Credentials/EliminarCredencial, Auth/AislamientoRecursos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #60 |

### 3.10 Usuarios y roles — `UsuariosController` → `UsuarioService` (9)

Pantalla: Usuarios, detalle, alta y asignación de rol (necesaria para Comedores: administración y alcance).

| # | Método y ruta | Servicio | Pruebas | Consumidor | Clasificación | Motivo |
|---|---|---|---|---|---|---|
| 64 | `GET /api/usuarios` | `ListarAsync` | C: Users · I: Auth/BusquedaUsuarios, AlcanceIndependiente, RolesAdministrativos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Usuarios del tenant dentro del alcance |
| 65 | `GET /api/usuarios/{id}` | `ObtenerAsync` | C: Users · I: Auth/RolesAdministrativos, Auth/AislamientoRecursos | Ninguno hoy | **Adaptar (T, A, P)** · controlador: no · DUDA-2 resuelta: se conserva | La SPA no lo usa |
| 66 | `POST /api/usuarios` | `CrearAsync` | C: Users · I: Auth/AlcanceIndependiente, Auth/RolesAdministrativos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | El usuario nace en el tenant del creador |
| 67 | `PUT /api/usuarios/{id}` | `ActualizarAsync` | C: Users · I: Auth/RolesAdministrativos (verificado: `PutAsJsonAsync`, línea 85) | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #66 |
| 68 | `POST /api/usuarios/{id}/desbloquear` | `DesbloquearAsync` | C: Users · I: Auth/PasswordPolicy | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #66 |
| 69 | `GET /api/usuarios/{id}/roles` | `ListarRolesAsync` | C: Users · I: Auth/RolesAdministrativos, Auth/AlcanceIndependiente | UI Blazor | **Adaptar (T, A, P)** · **controlador: sí, probablemente** | Hoy hay 2 roles fijos (B-03). Con roles base, personalizados y permisos por módulo cambian el DTO y las reglas |
| 70 | `POST /api/usuarios/{id}/roles` | `AsignarRolAsync` | C: Users · I: Auth/RolesAdministrativos, Auth/AlcanceIndependiente | UI Blazor | **Adaptar (T, A, P)** · **controlador: sí, probablemente** | Igual que #69. Además el cambio debe surtir efecto en la siguiente solicitud (B-02), no tras 60 minutos |
| 71 | `POST /api/usuarios/{id}/roles/{asignacionId}/finalizar` | `FinalizarRolAsync` | C: Users · I: Auth/RolesAdministrativos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #70 (revocación inmediata) |
| 72 | `POST /api/usuarios/{id}/roles/{asignacionId}/renovar` | `RenovarRolAsync` | C: Users · I: Auth/RolesAdministrativos | UI Blazor | **Adaptar (T, A, P)** · controlador: no | Igual que #70 |

## 4. Resumen de endpoints

| Controlador | Endpoints | Reutilizar | Adaptar | Retirar | Duda resuelta | Duda diferida |
|---|---|---|---|---|---|---|
| Auth | 3 | 1 | 2 | 0 | 0 | 1 (#1 → 002) |
| Compañías | 7 | 0 | 7 | 0 | 1 (#5) | 0 |
| Unidades organizativas | 6 | 0 | 6 | 0 | 1 (#13) | 0 |
| Áreas de acceso | 8 | 0 | 8 | 0 | 1 (#19) | 0 |
| Permisos | 4 | 0 | 4 | 0 | 1 (#26) | 0 |
| Evaluación de acceso | 1 | 0 | 1 | 0 | 1 (#29) | 0 |
| Maestros | 15 | 0 | 15 | 0 | 0 | 15 (#30-44 → 003) |
| Personas | 15 | 0 | 14 + #59 sin clasificar | 0 | 1 (#46) | 1 (#59 → 005) |
| Credenciales | 4 | 0 | 4 | 0 | 0 | 0 |
| Usuarios | 9 | 0 | 9 | 0 | 1 (#65) | 0 |
| **Total** | **72** | **1** | **70 + 1 sin clasificar** | **0** | **7** | **17** |

Aprobado sin reservas: las filas que no dependen de una duda diferida (72 − 17 = 55 endpoints). Las 17 con duda diferida conservan la clasificación **propuesta**, que se confirmará en su especificación.

Comprobación: `grep -c "\[Http"` sobre los 10 controladores da 3 + 7 + 6 + 8 + 4 + 1 + 15 + 15 + 4 + 9 = **72**, igual que la línea base y D-FE-01 (verificado el 2026-10-31).

**Ningún endpoint se retira.** Motivo: todos tienen consumidor (la SPA los usa y D-FE-01 manda reconstruir todas las pantallas de Etapa 1) o los sostienen pruebas de seguridad. Los 6 GET por id se conservan (DUDA-2 resuelta); el único candidato a retiro que queda es #59 (DUDA-5, diferida).

## 5. Casos dudosos

### 5.1 Resueltos (usuario, 2026-10-31)

| Id | Endpoints | Decisión |
|---|---|---|
| DUDA-2 | #5, #13, #19, #26, #46, #65 (GET por id) | **Se conservan los 6** y se adaptan. Siguen sosteniendo las pruebas de aislamiento (`Auth/AislamientoRecursos`) |
| DUDA-4 | #29 evaluación de acceso | **Confirmado**: la evaluación de acceso físico queda solo como pantalla de Etapa 1 y **no se reutiliza en Comedores** |

### 5.2 Diferidos, sin decidir

No se decide aquí. Cada duda pasa a la especificación indicada; hasta entonces, las filas afectadas mantienen la clasificación **propuesta**, no aprobada.

| Id | Endpoints o pruebas | Pregunta | Opciones | Propuesta (no aprobada) | Pasa a |
|---|---|---|---|---|---|
| DUDA-1 | #1 login | ¿El host Blazor sigue llamando a `POST /api/auth/login` y guarda el JWT en el servidor, o la autenticación pasa a cookie en Blazor con otro mecanismo hacia la API? | a) mantener login JWT, el host Blazor lo usa; b) sustituirlo por otro flujo y retirar éste | a), por ser el menor cambio | **Especificación 002** (Q-26 y Q-29) |
| DUDA-3 | #30-44 maestros (y #23-24, #39-41 por tipos de persona); `Masters/*`, `MaestrosContractTests` | ¿Los catálogos son de **plataforma** (compartidos, solo editables por el operador) o de **cada tenant**? | a) plataforma: solo cambia el permiso de edición; b) por tenant: cambian entidad, semilla y servicio genérico | Sin propuesta: decisión de producto. La semilla de Perú sugiere a) | **Especificación 003** |
| DUDA-5 | #59 estado efectivo | Sin consumidor hoy. ¿Lo necesitará Comedores o lo resuelve por contrato interno entre módulos? | a) adaptar y conservar; b) retirar | a) mientras no se descarte | **Especificación 005** |
| DUDA-6 | `UnitTests/Auth/AsignacionRolAdministrativoTests` (y #69-70) | ¿Cambian las reglas de asignación de rol al sustituir el catálogo cerrado de 2 roles por roles base, personalizados y permisos por módulo? | a) adaptar; b) reutilizar si se conserva el modelo de asignación | a) | **Especificación 003** (roles, I-6) |

## 6. Inventario de pruebas

Regla de D-FE-01: **la prueba sigue la suerte de su endpoint**. Como todos los endpoints se adaptan, casi todas las pruebas HTTP se adaptan, pero el cambio se concentra en los **fixtures**:

- `Fixtures/ApiFactory.CrearClienteAutenticadoAsync` **emite el token directamente con `ITokenService`** (no pasa por login) y el alcance va en sus claims. Cuando el alcance se resuelva por solicitud desde la BD, las pruebas que dependan de "lo que dice el token" cambiarán de comportamiento.
- `SembrarUsuarioAsync`, `SembrarCompaniaAsync`, `SembrarPersonaAsync`, `EscenarioUs5`, `EscenarioPermisos` y los `*Soporte` deben sembrar un tenant (y licencia) para que las pruebas sigan pasando.

Por eso se distingue **Adaptar (fixture)**: el archivo no debería cambiar si se adaptan los ayudantes; y **Adaptar (contenido)**: el archivo cambia.

### 6.1 Pruebas de contrato (15 archivos, 140 métodos)

| Archivo | Métodos | Endpoints | Clasificación | Motivo |
|---|---|---|---|---|
| `AuthContractTests` | 6 | #1-3 | **Adaptar (contenido)** | `LoginResponse` / `SesionActual` cambiarán con tenant y permisos |
| `CompaniasContractTests` | 10 | #4-10 | Reutilizar · *revisar* | Las rutas y DTOs no deberían cambiar si el tenant sale de la identidad |
| `UnidadesOrganizativasContractTests` | 10 | #11-16 | Reutilizar · *revisar* | Igual |
| `AreasAccesoContractTests` | 11 | #17-22 | Reutilizar · *revisar* | Igual |
| `AreaAccesoTipoPersonaContractTests` | 8 | #23-24 | Reutilizar · *revisar* | Igual |
| `PermisosContractTests` | 15 | #25-28 | Reutilizar · *revisar* | Igual |
| `EvaluacionAccesoContractTests` | 12 | #29 | Reutilizar · *revisar* | Sigue a su endpoint (DUDA-4 resuelta: se conserva) |
| `MaestrosContractTests` | 9 | #30-44 | Reutilizar · DUDA-3, diferida (003) | Cambia solo si los catálogos pasan a ser por tenant |
| `PersonasContractTests` | 9 | #45-48 | Reutilizar · *revisar* | Igual que Compañías |
| `HistorialPersonaContractTests` | 13 | #49-59 | Reutilizar · *revisar* | La parte de #59 depende de DUDA-5, diferida (005) |
| `CredencialesApiContractTests` | 6 | #60-63 | Reutilizar · *revisar* · **posible duplicado** | Cubre las mismas rutas que `CredencialesContractTests`; ¿se fusionan? (no se decide aquí) |
| `CredencialesContractTests` | 10 | #60-63 | Reutilizar · *revisar* | Igual |
| `UsersContractTests` | 12 | #64-72 | **Adaptar (contenido)** | DTO de asignación de rol cambia con B-03 |
| `OpenApiSnapshotTests` | 6 | todos | **Adaptar (contenido)** | Compara contra `specs/001-…/contracts`. Si la Etapa 1 cerrada no se edita, hará falta un contrato nuevo (`specs/002-…`) y apuntar la prueba a él |
| `AuditFieldsNotExposedTests` | 3 | todos | **Adaptar (contenido)** | Añadir `TenantId` (o equivalente) a los campos que ningún `Request` debe aceptar: el tenant sale de la identidad, nunca del cuerpo |

*Revisar* = se propone reutilizar, pero se confirma cuando exista el contrato de `specs/002-…`.

### 6.2 Pruebas de integración (70 archivos con pruebas, 509 métodos)

| Archivo(s) | Métodos | Endpoints | Clasificación | Motivo |
|---|---|---|---|---|
| `Auth/LoginTests` | 10 | #1, #3 | **Adaptar (contenido)** · DUDA-1, diferida (002) | Login y sesión con tenant |
| `Auth/PasswordPolicyTests` | 9 | #1, #2, #68 | Adaptar (fixture) | |
| `Auth/AislamientoRecursosTests` | 9 | 18 endpoints, verificados por método: #4, #5, #13, #15, #19, #21, #46, #49, #51, #53, #54, #56, #59, #60, #61, #62, #63, #65 | **Adaptar (contenido)** | Añadir el caso "otro tenant → 404". DUDA-2 resuelta: los GET por id de los que depende se conservan |
| `Auth/AlcanceIndependienteTests` | 9 | #64, #66, #69-70 | **Adaptar (contenido)** | Debe probar que el alcance cambia **en la siguiente solicitud** (B-02) |
| `Auth/BusquedaUsuariosTests` | 7 | #64 | Adaptar (fixture) | |
| `Auth/RolesAdministrativosTests` | 19 | #64-67, #69-72 | **Adaptar (contenido)** | Catálogo de roles y permisos (B-03) |
| `Companies/CompaniasTests`, `CambioTipoCompaniaTests`, `RelacionContratistaPrincipalTests` | 9 + 7 + 12 | #4-10 | Adaptar (fixture) | Añadir en `RelacionContratistaPrincipal` el rechazo entre tenants |
| `OrgUnits/*` (4 archivos) | 12 + 6 + 5 + 4 | #11-16 | Adaptar (fixture) | |
| `AreaAccess/*` (5 archivos) | 15 + 12 + 6 + 6 + 5 | #17-24 | Adaptar (fixture) | |
| `Permissions/PermisosTests`, `ContencionPermisoFechaCivil`, `ContencionPermisosPersona`, `PermisosHistoricosVigenciaDiaria`, `RegistrosAnterioresRf082`, `VigenciaDiariaPermisos`, `PrecedenciaPermisos` | 25 + 7 + 12 + 7 + 11 + 9 + 7 | #25-29 | Adaptar (fixture) | |
| `Permissions/EvaluacionAccesoTests`, `EvaluacionAccesoContratista`, `GateCredencial`, `RevalidacionDinamica`, `CompaniaInactivaYZonaHoraria` | 19 + 5 + 8 + 4 + 5 | #29 | Adaptar (fixture) | Acceso físico; siguen a su endpoint, que se conserva solo para Etapa 1 (DUDA-4 resuelta) |
| `Permissions/EvaluacionAccesoPerformanceTests`, `People/PersonasPerformanceTests` | 1 + 2 | #29, #45 | Adaptar (fixture) | Medir de nuevo cuando el alcance se resuelva desde BD (posible coste extra por solicitud) |
| `Masters/MaestrosTests` | 11 | #30-44 | Adaptar (fixture) · DUDA-3, diferida (003) | |
| `Masters/SeedDataTests` | 8 | ninguno (lee la BD) | Reutilizar · DUDA-3, diferida (003) | Cambia solo si los catálogos pasan a ser por tenant |
| `People/PersonasTests`, `PersonasBusquedaTests` | 12 + 11 | #45-48 | Adaptar (fixture) | |
| `People/HistorialPersona`, `CeseExplicitoPertenencia`, `RenovacionPertenencia`, `PreservacionHistorico`, `AislamientoPertenencias`, `RevocacionFechaFutura`, `RevocacionMultiPrincipal`, `RevocacionPorCambioCompania`, `Rf082RenovacionYCascada` | 10 + 6 + 11 + 6 + 3 + 6 + 4 + 6 + 2 | #49-52 (+ #27, #61) | Adaptar (fixture) | Cascada de revocación: seguridad y vigencias (principio VII) |
| `People/ContextoOperativoCasoA`, `ContextoOperativoCasoB`, `ContextosMultiplesSimultaneos`, `FechasObligatorias`, `ContencionTemporal` | 6 + 6 + 7 + 8 + 7 | #53-56 | Adaptar (fixture) | |
| `People/AsignacionUnidadOrganizativaTests` | 9 | #55-56 | Adaptar (fixture) | |
| `People/PerfilesPersonaTests`, `ContencionPerfilesTests` | 7 + 7 | #57-58 | Adaptar (fixture) | |
| `People/EstadoEfectivoTests` | 8 | #59 (+ #51, #56, #58) | Adaptar (fixture) · DUDA-5, diferida (005) | |
| `People/CredencialesAsignacionTests`, `Credentials/*` (4 archivos) | 8 + 7 + 8 + 6 + 6 | #60-63 | Adaptar (fixture) | |
| `Auditing/AuditoriaTests`, `TrazabilidadTests` | 7 + 3 | #6-7, #9-10, #27-28, #47-48, #50 | **Adaptar (contenido)** | La auditoría debe guardar el tenant del momento (D8, B-06) |
| `Security/EndurecimientoSeguridadTests` | 7 | todos (reflexión) | **Adaptar (contenido)** | Hoy exige `CompaniaScope` en todo endpoint de datos; con permisos por módulo cambia la política esperada. Añadir "sin tenant → denegado" |
| `Persistence/TriggersNoSolapamientoTests` | 9 | ninguno (BD; usa `EscenarioUs5` para sembrar) | Reutilizar · fixture | Prueba triggers SQL, no endpoints |
| `Persistence/VigenciaObligatoriaYContencionTests` | 5 | ninguno | Reutilizar | Prueba esquema y SQL |
| `Fixtures/SqlServerFixtureSmokeTests` | 4 | ninguno | Reutilizar | Infraestructura de pruebas |

Ninguna prueba de integración se propone retirar, por la misma razón que los endpoints.

### 6.3 Pruebas de unidad (11 archivos, 110 métodos)

No llaman a la API; prueban dominio y validadores. No siguen la suerte de ningún endpoint.

| Archivo | Métodos | Clasificación | Motivo |
|---|---|---|---|
| `Auth/AsignacionRolAdministrativoTests` | 9 | **DUDA-6, diferida (003)** · propuesta: adaptar | Si B-03 sustituye el catálogo cerrado de 2 roles, las reglas de asignación cambian |
| `Auth/PasswordPolicyValidatorTests` | 9 | Reutilizar | |
| `Common/CierreDeVigenciaTests` | 7 | Reutilizar | |
| `Hierarchy/JerarquiaCicloValidatorTests` | 11 | Reutilizar | |
| `People/ContencionFechaCivilPermisoTests` | 3 | Reutilizar | |
| `People/ContencionTemporalValidatorTests` | 8 | Reutilizar | |
| `People/RevocacionServiceTests` | 9 | Reutilizar | |
| `People/VigenciaNormalizationTests` | 8 | Reutilizar | |
| `Permissions/EvaluadorDeAccesoTests` | 29 | Reutilizar | Acceso físico; no se reutiliza para Comedores |
| `Permissions/ReglaContencionPermisoTests` | 2 | Reutilizar | |
| `Permissions/VigenciaDiariaPermisoTests` | 15 | Reutilizar | |

### 6.4 Fuera de este inventario

Las pruebas del frontend (Vitest, unos 199 casos; Playwright, 9 pruebas en 5 especificaciones) son de la SPA React y se sustituyen por pruebas de la UI Blazor (D-FE-01, "Por diseñar"). Se retiran con la SPA cuando haya paridad verificada de cada pantalla ([paridad-pantallas.md](paridad-pantallas.md)), no por este inventario.

### 6.5 Pruebas que faltan

Ver §7: cuatro pruebas obligatorias que hoy no existen.

## 7. Pruebas obligatorias que faltan — para la especificación 003

Hoy **no existe ninguna** de estas pruebas (verificado: no hay concepto de tenant ni de licencia en el código, y el alcance se lee del token). Son obligatorias por el principio VII (seguridad, alcance y tenant) y necesarias para cerrar B-01, B-02 y B-04. Se llevan a la **especificación 003**; aquí no se crean.

| # | Prueba | Qué debe demostrar | Brecha | Endpoints afectados |
|---|---|---|---|---|
| PO-1 | Aislamiento entre tenants | Un usuario de un tenant no lee ni escribe recursos de otro tenant, aunque conozca su id (respuesta 404, como el aislamiento actual por alcance) | B-01 | Todos los que leen o escriben datos (#3-72) |
| PO-2 | Tenant no determinable → denegado | Si el tenant no puede obtenerse de la identidad autenticada, la solicitud se deniega (falla cerrado) | B-01, D8-05 | Todos los autenticados |
| PO-3 | Alcance resuelto por solicitud | Una asignación, finalización o renovación de rol o de alcance surte efecto **en la siguiente solicitud con el mismo token**, sin esperar a su caducidad | B-02, D-FE-01 | Todos; en especial #69-72 |
| PO-4 | Licencia revalidada en cada operación | Con la licencia del módulo vencida o ausente, cada operación del módulo se deniega, también en una sesión ya abierta | B-04, D8-02 | Los del módulo licenciado (Comedores y los que se declaren licenciables) |

Punto de partida para escribirlas: `Auth/AislamientoRecursosTests` (patrón de 404 fuera de alcance) y `Auth/AlcanceIndependienteTests`. El fixture debe dejar de llevar el alcance en el token (§6).

## 8. Qué queda abierto

1. DUDA-1 (especificación 002), DUDA-3 y DUDA-6 (especificación 003) y DUDA-5 (especificación 005). Hasta que se decidan, las filas afectadas siguen en **propuesta**.
2. Las pruebas PO-1 a PO-4 (§7), en la especificación 003.
3. Antes de mover nada a la carpeta de retiro (hoy no hay ningún candidato), etiqueta de Git (Q-30).
