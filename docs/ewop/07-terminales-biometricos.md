# EWOP — Integración con terminales biométricos (§9)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Vigente, **con actualizaciones del punto 8**: R8-50 a R8-53 (autonomía de terminales sin verificar, enrolamiento central, consumo observado, registro de todos los eventos) y prohibición de acceso directo a la BD. Ver multitenancy/requisitos-declarados-R8.md y D8-05

---


La recomendación es crear una capa de contratos de integración y adaptadores específicos por proveedor/modelo, sin propagar las peculiaridades de cada fabricante al dominio de asistencia.

```text
Time Attendance
       |
       v
Device Integration Contracts
       |
       +--> IDEMIA Adapter
       +--> HID Adapter
       +--> ZKTeco Adapter
       +--> Other Vendor/Model Adapters
```

Este esquema es conceptual; no implica que todos los adaptadores ya existan ni que todos los dispositivos sean compatibles.

### Principios

- El dominio de asistencia debe consumir conceptos normalizados, no detalles de protocolo del fabricante.
- El adaptador gestiona particularidades técnicas del dispositivo y transforma eventos a contratos internos.
- Las diferencias por modelo, versión, firmware, protocolo y capacidades deben validarse.
- Definir idempotencia, deduplicación, timestamps, zona horaria, secuencias, eventos tardíos, reintentos y observabilidad al especificar la ingestión.
- Diseñar el manejo de desconexiones y sincronización si los requisitos de operación lo exigen.
- Evitar asumir que es necesario centralizar plantillas biométricas. Evaluar si el dispositivo solo entrega un identificador/resultado o si se necesita tratar datos biométricos más sensibles.
- Revisar privacidad, seguridad, acceso, retención y normativa aplicable antes de cerrar la arquitectura de biometría.

Los detalles técnicos y funcionales de cada fabricante todavía necesitan investigación y especificación; no deben inventarse protocolos ni capacidades.
