# EWOP — Decisiones pendientes y próximos pasos (§12 y §13)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Lista original. **Tras el punto 8 quedan decididos o acotados los ítems 1, 2, 3 y 6 y parcialmente el 15**; persisten el 4, 13, 14 y 16 a 20 (ver multitenancy/impacto-en-otras-secciones.md)

---


Estas cuestiones deben resolverse antes de cerrar la arquitectura de referencia:

### Prioridad alta

1. **Modelo de despliegue:** SaaS centralizado, dedicado por cliente o híbrido.
2. **Aislamiento de datos:** base compartida con aislamiento lógico, base por tenant o infraestructura dedicada.
3. **Modelo organizativo:** definición formal de tenant, mandante/principal, contratista, instalación/sede, área y sus relaciones.
4. **Stack de implementación:** confirmar versiones de .NET, framework frontend, persistencia, autenticación y despliegue.
5. **Límites de módulos/proyectos:** validar cuáles son proyectos de código reales y cuáles deben permanecer como componentes internos.
6. **Estrategia de identidad/autorización:** modelo de usuarios, roles, permisos y contexto de tenant.

### Prioridad funcional

7. Flujos y reglas de acreditación, vencimientos, aprobaciones, suspensión y reactivación.
8. Modelo de credenciales: tarjeta, QR, RFID/NFC, identificadores externos, emisión y revocación.
9. Modelo de autorización física: recursos, puertas, áreas, horarios, reglas y eventos.
10. Reglas de asistencia: marcaciones, turnos, excepciones, conciliación y reportes.
11. Reglas de comedor: servicios, ventanas horarias, consumos permitidos, precios/subsidios y excepciones.
12. Requisitos de integración por fabricante/modelo de terminal.
13. Política de privacidad, seguridad y retención para datos de identidad y biometría.
14. Requisitos no funcionales: disponibilidad, rendimiento, volumen de eventos, recuperación, observabilidad y auditoría.
15. Modelo comercial y de licenciamiento: módulos, capacidades, límites y cambios de suscripción.

### Prioridad de operación

16. Estrategia de actualización y migración de esquema.
17. Observabilidad, logs, métricas, trazas y alertas.
18. Estrategia de backups/restore y recuperación ante desastres.
19. CI/CD, entornos, secretos y configuración por tenant.
20. Criterios que justificarían extraer un módulo a un servicio independiente.

## 13. Próximos pasos recomendados

Orden sugerido para continuar sin generar código prematuramente:

### Paso 1 — Verificar el estado real del repositorio

- Revisar la rama actual, estado del working tree, ramas remotas y PRs.
- Confirmar si `docs/final-delivery` fue publicada y si el PR final se abrió/fusionó.
- Inspeccionar los archivos de especificación y documentación de EAC.
- Preservar el historial y las decisiones que ya están en el repo.

### Paso 2 — Confirmar el producto y el nombre

- Decidir si EWOP será el nombre de trabajo.
- Mantener EAC como módulo/proyecto original si tiene sentido, sin renombrar todo automáticamente.
- Antes de un uso comercial, comprobar disponibilidad de marca y dominio; no se ha verificado todavía.

### Paso 3 — Resolver modelo de despliegue y aislamiento

Elegir explícitamente:
- SaaS centralizado, dedicado o híbrido.
- Aislamiento lógico, base por tenant o infraestructura dedicada.

Documentar razones, consecuencias y requisitos que motivan la elección.

### Paso 4 — Definir el modelo organizativo

Modelar relaciones entre tenant, empresas, mandante/principal, contratistas, personas, instalaciones, áreas, usuarios y suscripciones. No empezar por crear tablas: primero fijar definiciones y reglas.

### Paso 5 — Delimitar módulos y contratos

Para cada módulo:
- Responsabilidad.
- Entidades/objetos bajo su propiedad.
- Casos de uso.
- Datos que expone a otros módulos.
- Eventos que publica/consume.
- Dependencias permitidas.
- Límites de transacción.
- Pruebas de aceptación.

### Paso 6 — Diseñar la solución de referencia

- Confirmar el stack.
- Diseñar proyectos .NET y referencias.
- Diseñar host/API y registro de módulos.
- Definir autenticación, autorización, tenant context y entitlements.
- Diseñar persistencia y migraciones.
- Definir integración de dispositivos mediante contratos y adaptadores.
- Diseñar frontend modular y rutas.
- Definir pruebas unitarias, integración, contrato, seguridad y aislamiento multi-tenant.

### Paso 7 — Crear una base vertical mínima

Implementar solo después de aprobar decisiones anteriores:
- Arranque de host/API.
- Autenticación y contexto de tenant según la estrategia elegida.
- Un módulo vertical representativo.
- Un contrato de módulo.
- Persistencia y pruebas.
- Verificación de autorización y entitlement en backend.
- CI básica.

Evitar crear todos los proyectos vacíos sin una estructura de dependencias validada.

### Paso 8 — Planificar la evolución por incrementos

Orden funcional sugerido para discutir, no aprobado:
1. Consolidar el módulo de acceso físico existente.
2. Establecer identidad operativa y credenciales como base compartida.
3. Añadir acreditación/compliance y su relación con elegibilidad.
4. Incorporar asistencia con adaptadores de terminales.
5. Incorporar consumos de comedor.
6. Añadir licenciamiento por módulo, configuración y reportes de forma transversal según los requisitos.

El orden final debe basarse en prioridades de negocio, dependencias reales y estado del código EAC existente.
