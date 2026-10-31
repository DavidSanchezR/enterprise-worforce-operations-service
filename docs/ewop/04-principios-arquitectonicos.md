# EWOP — Principios arquitectónicos (§5)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Dirección propuesta (monolito modular, monorepo); no autoriza implementación sin validar decisiones pendientes

---


### 5.1. Monolito modular primero

La recomendación principal fue iniciar con un **monolito modular**:

- Un producto y una solución principal.
- Módulos con límites explícitos.
- Dependencias controladas.
- Contratos claros para comunicación entre módulos.
- Despliegue inicial sencillo en comparación con una arquitectura de microservicios.
- Posibilidad de extraer componentes más adelante si hay razones operativas concretas.

No crear microservicios únicamente porque existan cinco módulos funcionales o porque distintos clientes contraten diferentes módulos.

### 5.2. Monorepo

La recomendación para la etapa actual es mantener el backend y los módulos del producto en un repositorio principal (monorepo), con proyectos separados por responsabilidad cuando sea útil. Esto facilita coherencia de contratos, refactorizaciones coordinadas, pruebas y versionado del producto.

La organización final del frontend (mismo repositorio o subdirectorio claramente delimitado) debe definirse según las herramientas y el stack elegido. El ejemplo propuesto lo coloca en `frontend/`.

### 5.3. Tres conceptos distintos

No hacer coincidir obligatoriamente estas tres dimensiones:

1. **Módulo de negocio:** responsabilidad funcional, por ejemplo asistencia.
2. **Proyecto de código:** unidad de compilación y límite de dependencias, por ejemplo `TimeAttendance.Domain`.
3. **Unidad de despliegue:** aplicación o proceso publicado y ejecutado.

Un módulo de negocio no necesita ser un microservicio ni una aplicación web independiente.

### 5.4. Cohesión alta y acoplamiento bajo

- Cada módulo debe ser responsable de su propio modelo y reglas de negocio.
- Evitar que un módulo escriba directamente en las tablas internas de otro.
- Evitar referencias indiscriminadas entre modelos de dominio.
- Utilizar contratos explícitos, interfaces y/o eventos de aplicación donde se necesite comunicación entre módulos.
- Mantener `SharedKernel` pequeño; no convertirlo en un repositorio de todas las clases compartidas.
- Las abstracciones compartidas deben existir por una razón clara, no solo para evitar duplicación superficial.

### 5.5. Capacidad de evolución

La arquitectura debe permitir separar una capacidad en un servicio independiente cuando haya motivos demostrables, como:
- Necesidad de escalar de manera independiente.
- Requisitos distintos de disponibilidad o mantenimiento.
- Equipos y ciclos de entrega autónomos.
- Aislamiento especial de integraciones.
- Requisitos regulatorios o de seguridad.
- Límites de dominio y datos suficientemente independientes.

La ingestión de eventos de terminales de asistencia se mencionó como posible candidata futura si el volumen, la disponibilidad o el aislamiento de integraciones lo justifican. Esto es una posibilidad, no una decisión de separación ya tomada.
