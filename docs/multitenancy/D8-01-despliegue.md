# D8-01 — Modelo de despliegue

> **Origen:** Punto 8 (multi-tenancy, licencias y aislamiento). Decisiones D8-01 a D8-05 aprobadas por el usuario.
> **Estado:** Aprobada

---


- **Estado:** aprobada.
- **Contexto:** servir a clientes que aceptan nube y a otros que exigen infraestructura propia (R8-02), con operación de acceso físico sin conexión (R8-01) y con el proveedor como operador de todas las instalaciones (R8-03).
- **Alternativas evaluadas:** SaaS centralizado puro; instalación dedicada para todos; híbrido, entendido como dos ejes independientes (plano central SaaS o dedicado, y componente local en sede).
- **Decisión:** un solo producto y una sola base de código, agnóstico al despliegue, con tres formas: plano central SaaS multi-tenant operado por el proveedor; plano central dedicado (un tenant, infraestructura exigida por el cliente) operado por el proveedor; y un componente local en sede solo donde los terminales no sean autónomos. El componente local es una unidad de despliegue, no un microservicio.
- **Alternativas descartadas por el momento:** SaaS puro como única oferta (no cubre R8-01 ni R8-02); dedicado para todos (costo sin necesidad); instalación operada por el cliente (fuera de alcance por R8-03).
- **Justificación:** es la única combinación que cubre los requisitos declarados sin duplicar el producto.
- **Consecuencias arquitectónicas:** contexto de tenant válido para uno o muchos tenants; migraciones aplicables a N instalaciones; replicación al borde de un subconjunto de datos de autorización; licencia verificable sin conexión; actualización remota de componentes en sede.
- **Riesgos y mitigaciones:** divergencia entre formas de despliegue (automatización y pruebas en todas); costo operativo de N instalaciones dedicadas (criterio comercial por definir); sedes con conectividad limitada para actualizar (política de versiones por definir).
- **Preguntas pendientes:** autonomía real por modelo de terminal; obsolescencia tolerada en revocaciones; duración máxima de autonomía local; requisitos regulatorios y de residencia de datos; si hace falta un componente intermedio en sede para comunicación y reenvío de eventos.
