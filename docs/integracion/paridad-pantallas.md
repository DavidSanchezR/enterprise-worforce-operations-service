# Paridad de pantallas — React a Blazor Server

> **Consultar cuando:** se migra una pantalla o se decide si React puede retirarse.
> **Certeza:** inventario hecho por **nombres de archivo** de `frontend/src/features` (el código no se leyó). Los estados los actualiza quien migra, con evidencia. "Necesaria para Comedores" es inferido.
> **Regla:** una pantalla React se retira solo cuando su fila está en *Paridad verificada*.

Estados: Pendiente · En migración · Paridad verificada · React retirado.

| Área | Pantalla o componente React | Necesaria para Comedores | Estado |
|---|---|---|---|
| Shell | `app/AppShell.tsx` (navegación y marcador de Dashboard) | Sí | Pendiente |
| Auth | `LoginPage`, `CambiarPasswordPage` | Sí | Pendiente |
| Datos maestros | `MaestrosPage`, `MaestroFormulario` | Sí (catálogos de personas) | Pendiente |
| Compañías | `CompaniasPage`, `CompaniaFormulario`, `RelacionesDialogo` | Sí | Pendiente |
| Personas | `PersonasPage`, `PersonaFormulario` | Sí | Pendiente |
| Personas | `PersonaHistorialPage`, `PertenenciaContextoWizard`, `RenovarPertenenciaDialogo` | Sí (vínculo) | Pendiente |
| Personas | `PerfilesPersona`, `AsignacionUnidadOrganizativa` | Por confirmar (categoría del trabajador, Q-16) | Pendiente |
| Credenciales | `CredencialesPersona` | Sí (identidad operativa) | Pendiente |
| Usuarios y roles | `UsuariosPage`, `UsuarioDetalle`, `CrearUsuarioWizard`, `UsuarioFormulario`, `AsignarRolDialogo` | Sí (administración y alcance) | Pendiente |
| Unidades organizativas | `UnidadesOrganizativasPage` (árbol) | No | Pendiente |
| Áreas de acceso | `AreasAccesoPage` (árbol), `TiposPersonaPorArea` | No | Pendiente |
| Permisos | `PermisosPage`, `PermisoFormulario`, `SelectorSemanal` (rejilla semanal) | No | Pendiente |
| Evaluación de acceso | `EvaluacionAccesoPage` (15 pasos) | No | Pendiente |

**Puntos de atención de UI:** los controles de árbol deben operarse con teclado (constitución); la rejilla semanal de permisos y los asistentes de varios pasos son los componentes más complejos; las pantallas de personas, permisos y usuarios son las de mayor tamaño.
