# Comedores — Integración con la plataforma (punto 8)

> **Consultar cuando:** se conecta el módulo con tenant, licencias, permisos, configuración, auditoría, terminales u otros módulos.
> **Alcance (declarado 2026-10-30):** todas las reglas de tenant y licenciamiento aplican completas al módulo, incluidos entitlement, límites, gracia y cuarentena. Sin reportes, consumos manuales ni migraciones.
> **Certeza:** aplica decisiones **aprobadas** (D8-01 a D8-05, requisitos R8-nn) al módulo. Los nombres de permisos y la clasificación de operaciones son **propuestas** (inferidas) a validar. No fija proyectos .NET ni tecnologías.

## Tenant, entitlement y estado de la licencia

| Tema | Cómo aplica al módulo | Origen |
|---|---|---|
| Tenant | Todo dato del comedor pertenece al tenant (grupo empresarial); sin vínculos entre tenants | D8-02, R8-05, R8-06 |
| Entitlement | El módulo Dining & Consumption Management se contrata a nivel de tenant. Sin entitlement no hay acceso a sus casos de uso, aunque el usuario conozca la ruta | D8-04, R8-09 |
| Límites | Pueden contarse personas activas (vínculos no cerrados) y terminales; el módulo no define límites propios | R8-10, R8-26 |
| Licencia restringida | Tras la gracia, el comedor sigue operando con las reglas cargadas y los consumos nuevos van a cuarentena, sin descartarse | R8-12, R8-22, R8-53 |
| Histórico | Lectura y exportación del histórico siguen disponibles dentro del plazo de conservación | R8-21, R8-27 |

## Clasificación de operaciones (propuesta)

| Operación | Clase |
|---|---|
| Crear o modificar tipo de servicio, comedor o servicio de comedor | Amplía |
| Desactivar tipo de servicio, comedor o servicio de comedor | Reduce |
| Habilitar una contratista; asignar acceso (individual, masivo o por compañía) | Amplía |
| Deshabilitar una contratista; quitar acceso; asignar restricción | Reduce |
| Consultar consumo por persona (en pantalla) | Consulta o exportación de histórico |
| Registrar un consumo del tótem | Evento (ingesta); se rige por el ciclo de vida de la licencia, no se bloquea |

Cada caso de uso debe declarar su clase y los permisos que exige; sin declaración se deniega (D8-04, D8-05).

## Permisos propuestos (nombres provisionales)

Catálogo del módulo para componer roles base y personalizados (R8-18):

| Permiso | Para |
|---|---|
| comedores.catalogo.gestionar | Tipos de servicio, comedores y servicios de comedor |
| comedores.habilitacion.gestionar | Habilitar y deshabilitar contratistas |
| comedores.acceso.asignar | Asignar o quitar acceso (individual, masivo, compañía) |
| comedores.restriccion.gestionar | Asignar y levantar restricciones |
| comedores.consumo.consultar | Consultas de consumo |

El alcance (mandante, sede, contratista) se aplica aparte; un usuario de contratista solo ve su empresa (R8-14).

## Configuración funcional

- Precio, moneda, horario, consumo máximo y demás parámetros del servicio de un comedor son **configuración del punto operativo** (el comedor como área o punto operativo, inferido) con herencia: área, sede, mandante, tenant (R8-19, R8-20, R8-31).
- Los valores por mandante aplican a las reglas de sus personas, por ejemplo los servicios a los que tienen derecho; **no** a las áreas (R8-24).
- Cambiar la configuración funcional de un área por parte del soporte exige autorización del tenant (R8-35).

## Contexto del momento, eventos y ciclo de vida

- Cada consumo guarda sede, comedor, servicio, mandante, contratista, identidad operativa, centro de costo (si se confirma) y precio y moneda vigentes (R8-48).
- El consumo original se conserva; su evaluación (conforme u observado) es posterior (R8-51).
- El módulo debe implementar el **contrato común de ciclo de vida**: bloquear los datos de una persona, exportar histórico y eliminar o devolver todos los datos del tenant, con evidencia (R8-27, R8-36, R8-41).

## Auditoría

Las operaciones sensibles listadas en [04-reglas-de-negocio.md](04-reglas-de-negocio.md) se auditan de forma **atómica** con la operación (R8-49).

## Terminales (tótems)

- El tótem se integra por **adaptador y contrato de aplicación**, nunca por acceso directo a la BD (R8-50, D8-05).
- El equipo se identifica con su identidad de dispositivo registrada, de la que se deriva el tenant.
- La fuente muestra un tipo de control asociado a un fabricante de terminales. Las capacidades reales por modelo y firmware **no están verificadas**.
- Un evento de un dispositivo no registrado no puede asignarse a un tenant; su tratamiento está pendiente en el punto 8.

## Dependencias con otros módulos y con el núcleo organizativo

| Necesita | De quién | Estado |
|---|---|---|
| Persona, vínculo, identidad operativa, contratista, mandante, sede, área | Núcleo organizativo (R8-47) | Por construir |
| Credencial y su estado | Identity & Credential Management | Frontera con el núcleo sin cerrar |
| Elegibilidad de acreditación | Workforce Accreditation & Compliance | Contrato por definir |
| Evaluación de entitlement, permisos, configuración, auditoría | Platform | Por construir |
| Reserva de cupo por activar un vínculo | Licenciamiento | Contrato transaccional (R8-46) |

## Frontend

La interfaz del módulo será **Blazor Server** (ver [D-FE-01](../../decisiones/D-FE-01-frontend-blazor-server.md)). La UI llama a la capa de aplicación; los permisos y entitlements se validan en cada operación del backend, no en los componentes.
