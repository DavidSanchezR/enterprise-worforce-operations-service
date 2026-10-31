# EWOP — Módulos funcionales (§3)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Alcance funcional planteado; reglas detalladas de cada módulo siguen por especificar

---


Los siguientes cinco módulos son el alcance funcional planteado.

### 3.1. Workforce Accreditation & Compliance
**Nombre en español:** Acreditación Documentaria y Cumplimiento.

Responsabilidad conceptual:
- Administrar y validar documentación de personas y contratistas.
- Determinar si una persona cumple las condiciones documentarias requeridas.
- Permitir incorporación, habilitación, reactivación o cambio de estado operativo cuando se cumplan las reglas aplicables.
- Proveer un estado de elegibilidad que otros módulos puedan consultar.

Principio importante: la acreditación determina una condición de elegibilidad, pero no debe conceder automáticamente todos los permisos de acceso. La elegibilidad puede ser requisito necesario, pero no suficiente para autorizar una acción concreta.

Los tipos documentarios, reglas de vencimiento, flujos de aprobación, excepciones y responsables aún deben especificarse.

### 3.2. Identity & Credential Management
**Nombre en español:** Gestión de Identidades y Credenciales.

Responsabilidad conceptual:
- Gestionar la identidad operativa de las personas dentro de la plataforma.
- Administrar credenciales de identificación.
- Producir o gestionar fotochecks/carnés por empresa, área u otros criterios.
- Considerar tecnologías como QR, RFID y NFC/contactless.
- Vincular de forma controlada personas, identidades, credenciales y los sistemas consumidores.

QR, RFID y NFC no deben modelarse como categorías necesariamente equivalentes o mutuamente excluyentes: RFID es una familia tecnológica y NFC es una tecnología relacionada de corto alcance.

No se ha decidido qué tecnología se utilizará primero ni si la plataforma imprimirá tarjetas directamente o se integrará con software/hardware de impresión.

### 3.3. Physical Access Management
**Nombre en español:** Gestión de Accesos Físicos.

Es el núcleo del proyecto original EAC.

Responsabilidad conceptual:
- Controlar y registrar intentos/eventos de acceso físico.
- Aplicar reglas de autorización a puntos, puertas, áreas o instalaciones, según el alcance que se detalle.
- Tener en cuenta identidad, credencial, estado de acreditación, permisos, horarios y configuración que correspondan.
- Mantener trazabilidad/auditoría de decisiones y eventos.

Los detalles actuales del dominio EAC se encuentran en el repositorio del proyecto; este documento no reemplaza sus especificaciones existentes.

### 3.4. Time & Attendance Management
**Nombre en español:** Gestión de Asistencia y Jornada.

Responsabilidad conceptual:
- Recibir y procesar marcaciones de asistencia desde distintos terminales/proveedores.
- Integrarse con dispositivos de fabricantes como IDEMIA, HID y ZKTeco.
- Mantener eventos de marcación y procesarlos según las reglas de asistencia que se definan.
- Separar la captura técnica de eventos de la interpretación funcional de jornada/asistencia.

Las capacidades, protocolos y comportamientos dependen del modelo y versión del dispositivo. No asumir que todos los equipos de un fabricante ofrecen las mismas interfaces.

### 3.5. Dining & Consumption Management
**Nombre en español:** Gestión de Comedores y Consumos.

Responsabilidad conceptual:
- Gestionar consumos de desayuno, almuerzo, cena, servicio a la carta y bebidas.
- Aplicar ventanas horarias y reglas de servicio.
- Asociar cada consumo a la persona/identidad correspondiente y conservar su trazabilidad.
- Permitir configuración según cliente, instalación o comedor cuando corresponda.

Todavía deben especificarse límites de consumo, subsidios, cuotas, precios, pagos, integración con POS, excepciones, reglas por turno y reportes.

### Capacidades transversales propuestas

Se identificaron como preocupaciones transversales, sujetas a diseño detallado:

- Personas, empresas y contratistas.
- Estructura organizacional y asignaciones a instalaciones/áreas.
- Autorización y control de acceso a funciones.
- Multi-tenancy y aislamiento de información.
- Licenciamiento/entitlements de módulos.
- Auditoría y trazabilidad.
- Configuración por cliente.
- Integraciones externas y gestión de dispositivos.

No convertir automáticamente cada capacidad transversal en un proyecto independiente. Su ubicación debe responder a responsabilidades y dependencias reales.
