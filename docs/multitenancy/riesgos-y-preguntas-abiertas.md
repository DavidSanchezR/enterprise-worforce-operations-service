# Punto 8 — Riesgos consolidados y preguntas abiertas

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Vivo: actualizar cuando se cierre una pregunta o aparezca un riesgo

---


1. Fuga entre tenants por un fallo de filtrado: capas redundantes y pruebas automáticas de acceso cruzado.
2. Lectura permanente del soporte, y cambios sobre eventos y credenciales sin aprobación del tenant (R8-15, R8-35).
3. Datos biométricos centralizados (R8-52): máxima sensibilidad, eliminación en terminales con evidencia.
4. Conservación indefinida posible (R8-42, R8-45) frente al marco legal (§8.8).
5. Divergencia entre formas de despliegue y costo operativo de instalaciones dedicadas.
6. Obsolescencia de revocaciones en terminales desconectados.
7. Acoplamiento por transacción única entre módulos y núcleo organizativo como dependencia de todos.
8. Pérdida de eventos en terminales con memoria llena o desconectados mucho tiempo, frente a R8-53.
9. Tope de gracia sin excepción que obliga a restringir aun en disputas comerciales (R8-30).

### 8.10. Preguntas abiertas consolidadas

**Negocio y legal:** valores del plazo de conservación, del plazo por defecto por persona, del tope de gracia y del período sin incidencias; anticipación del aviso previo; rol de encargado o responsable y normas de conservación; consentimiento biométrico; escala y volumen de eventos; modelo comercial y cobro por sede si se quisiera.

**Modelo y producto:** pertenencia de áreas; qué ve cada mandante de una contratista compartida; relación usuario-persona y cuentas con varios tenants; contratista sin mandante concreta; clave de unicidad de la persona; definición de la suspensión por entidad; catálogo completo de cambios del soporte; autorización sin usuario del tenant disponible; persona activa que pide bloqueo más allá de R8-38.

**Arquitectura:** frontera núcleo organizativo e Identity & Credential Management; auditoría de plataforma frente a la del tenant; conciliación del contexto de eventos del borde; desempate de consumos; dispositivos no registrados; granularidad de Platform; stack; componente intermedio en sede.

**Terminales:** capacidades reales por modelo y firmware (IDEMIA, ZKTeco, HID); búfer de eventos; obsolescencia tolerada en revocaciones; origen y formato de las plantillas por fabricante.
