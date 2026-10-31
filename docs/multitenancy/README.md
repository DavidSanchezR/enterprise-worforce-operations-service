# Punto 8 — Multi-tenancy, licencias y aislamiento de datos

> Reemplaza la sección 8 del contexto original (que figuraba como "pendiente de decisión formal"). Todo lo que sigue procede de la sesión de continuación del punto 8.

## Cómo leer esta carpeta

Empieza siempre por este archivo. Carga **solo** el archivo que necesites; cada uno es independiente y se entiende sin los demás.

> **Estado de este punto:** decisiones D8-01 a D8-05 **aprobadas por el usuario** en la sesión de continuación del punto 8. Esta sección reemplaza el contenido anterior del punto 8 ("pendiente de decisión formal") y conserva la terminología del resto del documento.
>
> **Convención de certeza usada en esta sección:**
> - **Declarado:** requisito o regla indicada por el usuario en la sesión (identificado como `R8-nn`).
> - **Inferido:** deducción técnica de quien redactó, derivada de lo declarado; requiere validación si no figura en una decisión aprobada.
> - **Verificado externo:** dato contrastado en una fuente pública citada; no sustituye asesoría legal.
> - **Pendiente:** pregunta abierta; no debe tratarse como decidida.
>
> **Alcance:** solo decisiones de multi-tenancy, licenciamiento y aislamiento. No define proyectos .NET, tecnologías, tablas ni código. El stack sigue pendiente (§12, ítem 4).

## Decisiones aprobadas

| ID | Tema | Estado | Idea central |
|---|---|---|---|
| D8-01 | Modelo de despliegue | Aprobada | Un producto, una base de código, agnóstico al despliegue: plano central SaaS multi-tenant o dedicado (un tenant), ambos operados por el proveedor, más un componente local en sede solo donde haga falta |
| D8-02 | Estrategia de aislamiento de datos | Aprobada | Aislamiento lógico estricto como línea base en todos los despliegues; ubicación portable del tenant (BD compartida, BD propia o instalación dedicada) sin cambiar la aplicación |
| D8-03 | Modelo organizativo y jerarquía | Aprobada | Modelo de entidades con relaciones explícitas, no un árbol; el tenant es el grupo empresarial |
| D8-04 | Entitlement, autorización y configuración funcional | Aprobada | Tres mecanismos separados, validados en backend; ciclo de vida de licencia con gracia, cuarentena y retención |
| D8-05 | Implicaciones para el monolito modular | Aprobada | Responsabilidades y límites de contexto de tenant, autorización, propiedad de datos, contratos, transacciones, auditoría y pruebas |

## Terminología fijada

- **Tenant** = grupo empresarial que contrata. **Empresa mandante**, **contratista**, **sede** y **área** son entidades distintas dentro del tenant.
- **Identidad operativa** = persona en un vínculo con un empleador (una por vínculo).
- **Entitlement** (qué módulos y límites tiene el tenant) ≠ **autorización** (qué puede hacer un usuario) ≠ **configuración funcional** (qué reglas y servicios están activos).

## Archivos

| Archivo | Consultar cuando |
|---|---|
| [requisitos-declarados-R8.md](requisitos-declarados-R8.md) | Cuando haga falta saber qué dijo exactamente el usuario o de qué requisito sale una decisión |
| [D8-01-despliegue.md](D8-01-despliegue.md) | Al decidir dónde y cómo se despliega el producto (SaaS, dedicado, componente local en sede) |
| [D8-02-aislamiento-datos.md](D8-02-aislamiento-datos.md) | Al diseñar persistencia, acceso a datos, migraciones, backups o pruebas de aislamiento entre tenants |
| [D8-03-modelo-organizativo.md](D8-03-modelo-organizativo.md) | Al modelar tenant, mandante, contratista, sede, área, persona, vínculo, identidad operativa o usuario |
| [D8-04-licencias-autorizacion-configuracion.md](D8-04-licencias-autorizacion-configuracion.md) | Al implementar licencias, límites, gracia, retención, roles, alcance, configuración o acceso del soporte |
| [D8-05-monolito-modular.md](D8-05-monolito-modular.md) | Al diseñar módulos, contratos entre módulos, transacciones, auditoría, propiedad de datos o pruebas |
| [marco-legal-peru.md](marco-legal-peru.md) | Antes de fijar plazos de conservación, políticas de eliminación, bloqueo o tratamiento de biometría |
| [riesgos-y-preguntas-abiertas.md](riesgos-y-preguntas-abiertas.md) | Al planificar qué validar o decidir a continuación, o al revisar riesgos antes de implementar |
| [impacto-en-otras-secciones.md](impacto-en-otras-secciones.md) | Al actualizar §6, §9 o §12 del contexto original, o al comprobar qué quedó decidido |
