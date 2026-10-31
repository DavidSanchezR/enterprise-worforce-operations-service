# Módulo Dining & Consumption Management (Comedores) — Índice

> **Fuente:** documento funcional "Migración 2Personnel Desktop a 2Personnel Web CLIENTE CONDESTABLE — Módulo Comedores" V 1.0 (WhiteLion Technology, 01/08/2025), 16 páginas. Describe el estado actual (AS IS) de un producto existente y lo que se migra.
> **Alcance de esta etapa (declarado 2026-10-30):** sin reportes, horario de interfaces, migraciones ni consumos manuales; tenant y licenciamiento aplican completos. Ver [D-ALC-01](../../decisiones/D-ALC-01-alcance-etapa-comedores.md).
> **Estado:** documentación derivada para planificar el desarrollo. **No hay decisiones de implementación aprobadas para este módulo** y no debe escribirse código antes de cerrar los prerrequisitos de [`07-backlog-y-dependencias.md`](07-backlog-y-dependencias.md).
> **Confidencialidad:** el documento fuente es de terceros y contiene datos de un cliente. Estos archivos **no reproducen capturas, nombres de personas ni de empresas**; los requisitos se expresan como comportamiento, sin copiar la interfaz del producto original.

## Cómo usar esta carpeta

Carga solo el archivo que necesites.

| Archivo | Consultar cuando | Líneas |
|---|---|---|
| [01-fuente-y-alcance.md](01-fuente-y-alcance.md) | Para saber qué se migra, qué no y qué dijo el cliente sobre cada punto | ~55 |
| [02-requisitos-funcionales.md](02-requisitos-funcionales.md) | Al planificar o implementar una funcionalidad concreta (RF-COM-nn) | ~90 |
| [03-modelo-de-dominio.md](03-modelo-de-dominio.md) | Al modelar entidades, atributos y su relación con el modelo organizativo del punto 8 | ~60 |
| [04-reglas-de-negocio.md](04-reglas-de-negocio.md) | Al implementar validaciones, restricciones y el flujo de consumo | ~45 |
| [05-reportes.md](05-reportes.md) | Al construir reportes y exportaciones | ~45 |
| [06-integracion-con-plataforma.md](06-integracion-con-plataforma.md) | Al conectar el módulo con tenant, licencias, permisos, configuración, auditoría y terminales | ~75 |
| [07-backlog-y-dependencias.md](07-backlog-y-dependencias.md) | Al planificar el trabajo y los prerrequisitos | ~55 |
| [08-preguntas-abiertas.md](08-preguntas-abiertas.md) | Antes de implementar: qué falta aclarar con el cliente y con el equipo | ~50 |

## Resumen en diez líneas

1. El módulo gestiona tipos de servicio (desayuno, almuerzo, cena), comedores, quién puede consumir qué, restricciones, consumos y reportes.
2. Una empresa contratista debe estar registrada y habilitada, con relación contractual vigente, antes de que su personal use el comedor.
3. El acceso al comedor se asigna por persona (individual o masivo) y, con una opción similar, por compañía.
4. Un tótem emite un ticket según horario y permiso de la persona; los consumos denegados quedan con su motivo.
5. La contingencia por consumos manuales (carga masiva y marcaciones manuales) **no entra** en esta etapa.
6. Los reportes (con los cambios que pidió el cliente) **no entran** en esta etapa; se conservan como registro.
7. Se vuelve a pedir la impresión de tickets de los tres servicios.
8. La migración de trabajadores e historial **no entra** en esta etapa.
9. El acceso del personal propio de la minera (practicante, obrero, empleado) está sin cerrar: el comentario del cliente quedó cortado en el PDF.
10. Aparecen conceptos que no están en el modelo organizativo aprobado (grupo, centro de costos, categoría y cargo del trabajador). Ver preguntas abiertas.

## Relación con las decisiones del punto 8

Este módulo se integra con D8-01 a D8-05 (ver [06-integracion-con-plataforma.md](06-integracion-con-plataforma.md)) y con la decisión de frontend [D-FE-01](../../decisiones/D-FE-01-frontend-blazor-server.md): la interfaz será Blazor Server.
