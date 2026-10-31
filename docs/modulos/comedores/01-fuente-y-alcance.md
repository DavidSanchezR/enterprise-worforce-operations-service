# Comedores — Fuente y alcance de la migración

> **Consultar cuando:** hay que saber qué se migra, qué no y qué dijo el cliente.
> **Certeza:** *Hecho del documento* salvo donde se indica *Inferido*. Los comentarios del cliente estaban incrustados en el PDF con una codificación desplazada y se decodificaron; el comentario de §5.6 quedó cortado.

## Datos de la fuente

| Dato | Valor |
|---|---|
| Documento | Migración 2Personnel Desktop a 2Personnel Web — Módulo Comedores, V 1.0, 01/08/2025 |
| Proveedor | WhiteLion Technology (jefe de proyecto y gerente de producto indicados en la hoja de control) |
| Cliente | Compañía Minera Condestable (CMC) |
| Alcance del proyecto | Migración de 2Personnel Desktop a la nube (Microsoft Azure), más un módulo de EPP |
| Unidades | Lima y operación minera (Cañete) |
| Inicio del proyecto | 05-06-2025. Marcha blanca y pase a producción: sin fecha |
| Módulos del proyecto | Administración, Identificación, Control de accesos, Asistencia, Campamentos, **Comedores**, Postulantes (acreditación), EPP |

**Observaciones de calidad del documento:** la sección 2 se titula "Conceptos generales de acreditación" (parece heredada de otro módulo); hay dos secciones numeradas "2"; los apartados 4.6 y 4.8 llevan el mismo título aunque el 4.8 trata de marcaciones manuales; dos figuras comparten el número 15.

## Qué se migra

| Sección | Funcionalidad | Decisión de migración | Comentario del cliente |
|---|---|---|---|
| 3.1 | Registro y habilitación de empresas contratistas | Se conserva | Conforme |
| 3.2 | Asignación de acceso por persona | Se conserva | Conforme, "la adecuada asignación de uso de comedor" |
| 4.1 | Tipos de servicio | Se migran los mismos servicios | Conforme |
| 4.2 | Comedores | Se migran los mismos | La inclusión es correcta |
| 4.3 | Horario de interfaces | **No se migra** (no se usa) | Correcto; no aplica en CMC, los horarios ya están establecidos de atención |
| 4.4 | Acceso por persona | Se contempla | Conforme |
| 4.5 | Acceso por compañía | Opción similar (hoy no se usa) | Conforme |
| 4.6 | Restricción del servicio | Opción similar | Conforme: permite restringir a los usuarios que consumen los servicios de alimentación |
| 4.7 | Consumo manual | Misma funcionalidad, con carga masiva por Excel para consumos no registrados por caída de energía | Conforme |
| 4.8 | Importación de marcaciones manuales | Opción similar si se requiere | Conforme |
| 4.9 | Consumo por grupo | Se cubre con el reporte de consumos con datos de compañías | Conforme |
| 4.10 | Consumo por persona | Misma funcionalidad | Conforme |
| 4.11 | Reporte de acceso a comedor | **No se considera** | Se puede requerir, pero como resumen consolidado por centro de costos por cada servicio de alimentos |
| 4.12 | Reporte de consumo de comedores | **No se considera** | Considerar como reporte en Excel, no PDF |
| 4.13 | Reporte consolidado de consumos | **No se considera** | Considerar como reporte en Excel, no PDF |
| 4.14 | Reporte detalle de consumo | Reportes similares con estructura distinta | Debe incluir categoría del trabajador (empleado, obrero o practicante) y cargo; agregar columnas DNI, Área y código personal |
| 4.15 | Reporte resumen mensual | No como tal; el área debe definir si lo usa | Queda como reporte en Excel |
| 4.16 | Reporte de consumos negados | Se tiene un reporte equivalente | Queda como reporte en Excel y PDF según lo requiera el usuario |

**Contradicciones a resolver:** en 4.11, 4.12 y 4.13 el documento dice "no se considera este reporte" y el cliente pide, en cambio, una versión (resumen por centro de costos o reporte en Excel). Hasta aclararlo no se sabe si esos reportes entran. Ver [08-preguntas-abiertas.md](08-preguntas-abiertas.md).

## Definiciones pendientes del documento (sección 5) y respuestas del cliente

| Punto | Pregunta del documento | Respuesta del cliente |
|---|---|---|
| 5.1 | ¿Se imprimen tickets de consumo con los nuevos tótems? | Se volverá a realizar la impresión de tickets de los tres servicios |
| 5.2 | Revisar si los reportes se usan; proponer uno o dos clave | Sin respuesta explícita; los comentarios por reporte están en la tabla anterior |
| 5.3 | ¿Se migran solo trabajadores activos? | Incluir personal cesado |
| 5.4 | Alcance de la migración histórica (completo o fecha de corte) | "Incluir historial de proyecto"; el alcance exacto no queda definido |
| 5.5 | Revisión funcional adicional | Sin respuesta |
| 5.6 | ¿Todo el personal propio tiene acceso a todos los comedores y los tres servicios sin restricción? | Respuesta **cortada** en el PDF: empieza con "Correcto…" y menciona restringir en el módulo; no es legible completa |

## Fuera de alcance de esta documentación

Los otros módulos del proyecto (acreditación de postulantes, EPP, campamentos, asistencia, control de accesos) no se analizan aquí. La relación con ellos se trata solo donde el comedor depende de sus datos (ver [06-integracion-con-plataforma.md](06-integracion-con-plataforma.md)).

## Recorte de alcance de esta etapa (declarado por el usuario, 2026-10-30)

Del documento fuente **no se construye**: reportes y exportaciones (4.9, 4.11 a 4.16 como reporte), horario de interfaces (4.3), migraciones (5.3, 5.4) y consumos manuales con su carga masiva e importación de marcaciones (4.7, 4.8). Lo que el cliente respondió sobre ellos se conserva arriba como registro. Detalle en [D-ALC-01](../../decisiones/D-ALC-01-alcance-etapa-comedores.md).
