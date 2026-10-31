# EWOP — Arquitectura de frontend propuesta (§7)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Propuesta; rutas y stack de frontend no definidos
> **AVISO:** la tecnología (React) quedó **reemplazada por Blazor Server en toda la solución** (ver [D-FE-01](../decisiones/D-FE-01-frontend-blazor-server.md)). Lo que sigue vale solo como referencia de organización por módulos contratados; las carpetas y librerías React ya no aplican.

---


Se recomendó inicialmente una aplicación web modular, organizada por funcionalidades, en vez de una aplicación web independiente por cada módulo:

```text
frontend/src/
├── app/
├── shared/
└── features/
    ├── accreditation/
    ├── identity-credentials/
    ├── physical-access/
    ├── time-attendance/
    └── dining-consumption/
```

- `app/`: composición, navegación y estructura principal de la aplicación.
- `shared/`: componentes y utilidades transversales realmente compartidos.
- `features/`: pantallas, componentes y lógica de presentación por módulo.

### URLs y navegación

Las rutas pueden representar módulos funcionales, por ejemplo:

- `/acreditacion`
- `/identidades-credenciales`
- `/accesos`
- `/asistencia`
- `/comedores`

Son ejemplos conceptuales; no están definidas como rutas definitivas.

La aplicación puede mostrar u ocultar opciones de navegación según:
1. Los módulos contratados por el tenant.
2. Los permisos del usuario.
3. La configuración funcional aplicable.

El backend debe repetir y hacer cumplir los controles. No confiar en el frontend para proteger datos u operaciones.

### ¿Cuándo separar las aplicaciones web?

Usar subdominios, frontends o aplicaciones distintas solo si aparecen razones reales, como:
- Equipos diferentes con autonomía de entrega.
- Ciclos de despliegue incompatibles.
- Necesidades distintas de seguridad o aislamiento.
- Experiencias de usuario radicalmente diferentes.
- Requisitos de disponibilidad o escalabilidad independientes.

La contratación separada de módulos, por sí sola, no obliga a separar los frontends.
