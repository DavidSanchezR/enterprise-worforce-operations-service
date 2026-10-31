# EWOP — Instrucciones para Claude (§14)

> **Origen:** Documento de contexto técnico original (exportación de la conversación previa). Las secciones § conservan la numeración original; ver README.md para el mapa.
> **Estado:** Vigente. Candidato a resumirse dentro de CLAUDE.md

---


Al recibir este documento:

1. **No reiniciar el análisis desde cero.** Utilizarlo como contexto inicial y luego verificar las afirmaciones relevantes contra el repositorio.
2. **No asumir que las propuestas son decisiones aprobadas.** Especialmente el nombre EWOP, el monolito modular, la estructura de carpetas, el despliegue y el aislamiento de datos.
3. **Primero verificar Git.** Confirmar estado actual de `docs/final-delivery`, `main`, `origin/main` y PR final. El último estado conocido es histórico y puede haber cambiado.
4. **Inspeccionar los artefactos SpecKit existentes** antes de crear especificaciones nuevas o alterar identificadores de tareas.
5. **Conservar los términos de dominio** y distinguir persona, identidad, credencial, identificador tecnológico, acreditación, autorización y evento.
6. **No proponer microservicios por defecto.** Partir del monolito modular salvo evidencia de una necesidad independiente de escalado, despliegue, seguridad, disponibilidad o propiedad.
7. **No generar estructura/código masivo antes de resolver** modelo de despliegue, aislamiento de datos, tenant y límites de módulos.
8. **Explicar comandos Git nuevos** antes de pedir al usuario que los ejecute: indicar propósito, efecto y si modifican archivos, historial o remoto.
9. **No afirmar que una acción se completó** sin salida de herramienta, evidencia de repositorio o confirmación explícita del usuario.
10. Al proponer cambios arquitectónicos, separar:
    - hechos observados en el repositorio;
    - decisiones confirmadas por el usuario;
    - recomendaciones técnicas;
    - preguntas que requieren decisión del usuario.
11. Priorizar una arquitectura mantenible, con bajo acoplamiento, alta cohesión, seguridad multi-tenant y contratos de integración claros.
12. Mantener el español como idioma principal, con nombres técnicos en inglés cuando sean estándar.

### Primer mensaje sugerido para continuar con Claude

> Lee este documento como contexto de continuidad del proyecto. Antes de modificar código, verifica el estado actual del repositorio `DavidSanchezR/enterprise-access-control`, confirma si el PR de `docs/final-delivery` fue publicado/abierto/fusionado y revisa los artefactos SpecKit existentes. Después, ayúdame a cerrar las decisiones pendientes de despliegue y aislamiento de datos, una por una. No implementes todavía una estructura completa de proyectos hasta que validemos el modelo de tenant y los límites de módulos.
