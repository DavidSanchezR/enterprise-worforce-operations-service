# EAC / EWOP — Control de acceso empresarial y plataforma de operaciones

Solución existente "Enterprise Access Control" (EAC, Etapa 1 cerrada) que evoluciona hacia EWOP (Enterprise Workforce Operations Platform). Etapa actual: **módulo Comedores**. Idioma de trabajo: **español**.

## Stack (verificado en el repositorio)

.NET 10, C# 13, ASP.NET Core 10 Web API, EF Core 10, SQL Server, FluentValidation, NodaTime, JWT Bearer, OpenAPI nativo. `TreatWarningsAsErrors` activo. Capas: `backend/src/` `Api`, `Application`, `Domain`, `Infrastructure`; pruebas de unidad, integración y contrato en `backend/tests/`. Frontend actual: SPA React en `frontend/` (se retira; ver abajo).

## Frontend: Blazor Server

- Decisión del usuario (D-FE-01): **toda la solución pasa a Blazor Server; la SPA React se retira**. Las pantallas de Etapa 1 se reconstruyen en Blazor. No hay convivencia permanente.
- Motivo: reducir la ingeniería inversa asistida por IA y la copia del sistema. **Reduce, no elimina**: no lo presentes como garantía absoluta.
- No generes pantallas nuevas en React ni retires el código React hasta tener paridad verificada de cada pantalla.
- La UI no es la barrera de seguridad: tenant, licencia, permiso y alcance se validan en la capa de aplicación **en cada operación**.
- La UI Blazor **consume la API por HTTP** con un cliente tipado; nunca accede a la BD ni a la capa de aplicación directamente. En componentes interactivos no uses `HttpContext` por `IHttpContextAccessor`; el host Blazor obtiene y renueva el token del usuario.
- La API resuelve tenant, licencia, permiso y alcance **por solicitud** (no confía en claims viejos del token).
- Endpoints y pruebas: se reutilizan o adaptan los que se puedan y el resto se retira a una carpeta **fuera de la solución** (Q-28 cerrada). No borres ni muevas nada sin el inventario aprobado (`docs/integracion/`) y una etiqueta de Git previa.

## Cómo se trabaja

1. Proceso: **Spec Kit** (`.specify/`, skills `speckit-*`). Lo nuevo va como especificación nueva (`specs/002-…`), no se edita la Etapa 1 cerrada. La constitución (`.specify/memory/constitution.md`) prevalece; cambios solo por enmienda explícita.
2. No cargues toda la documentación: abre `docs/README.md` y solo el archivo que corresponda.
3. El repositorio manda sobre los documentos. Verifica antes de afirmar.
4. No implementes sin decisiones aprobadas ni sin cerrar las preguntas bloqueantes. Empieza con una rebanada vertical mínima.
5. Distingue: declarado, inferido, verificado, pendiente. No marques nada como aprobado sin confirmación explícita.
6. Explica los comandos de Git antes de usarlos y no afirmes que algo está hecho sin evidencia.
7. Pruebas automatizadas obligatorias en seguridad, alcance, tenant, vigencias y auditoría (principio VII).

## Alcance de la etapa actual (D-ALC-01)

Entra: Comedores con tenant y licenciamiento completos; toda la UI en Blazor Server (incluida la reconstrucción de las pantallas de Etapa 1).
No entra: reportes, horario de interfaces, migraciones, consumos manuales (carga masiva e importación), asistencia, ampliación de acceso físico y acreditación.

## Reglas permanentes (D8-01 a D8-05)

- Tenant (grupo empresarial), mandante, contratista y sede son conceptos distintos. En EAC, "compañía principal" equivale a mandante.
- Licencia (entitlement), autorización (rol y alcance) y configuración funcional son mecanismos separados.
- El tenant sale de la identidad autenticada (usuario o dispositivo registrado); si no se puede determinar, se deniega.
- Los módulos no leen tablas de otros módulos; transacción coordinada única por tenant entre módulos.
- Auditoría atómica con la operación; cada registro guarda su contexto del momento.
- Los terminales nunca acceden a la BD; entran por adaptador y contrato. Ningún evento se descarta: los restringidos van a cuarentena.
- Cada caso de uso declara su clase de operación (amplía, reduce, neutra, consulta o exportación de histórico).
- Biometría: dato sensible; el enrolamiento es central.
- Cada módulo implementa el contrato de ciclo de vida de datos (bloquear, exportar, eliminar o devolver con evidencia).
- El `EvaluadorDeAcceso` actual es de acceso físico: no se reutiliza para validar consumos de comedor.

## Documentos de terceros

Los documentos del cliente son confidenciales. Describe comportamiento; no copies pantallas, nombres de personas ni de empresas, ni la interfaz de otro producto.

## Dónde mirar

`docs/README.md` (índice) · `docs/integracion/` (brechas EAC frente a plataforma) · `docs/multitenancy/` (punto 8) · `docs/modulos/comedores/` · `docs/decisiones/` · `docs/integracion/orden-de-ejecucion.md` (qué hacer y en qué orden) · `docs/integracion/prompts-de-arranque.md` y `prompts-migracion-pantallas.md` (prompts) · `docs/origen/` (contexto y punto 8 originales) · `docs/final/` y `specs/` (Etapa 1).
