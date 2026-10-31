# Prompts de migración de pantallas — React a Blazor Server

> **Consultar cuando:** se empieza o continúa la migración de la UI de Etapa 1. Complementa el paso 5 de [prompts-de-arranque.md](prompts-de-arranque.md).
> **Certeza:** propuesta. Requiere la base Blazor (especificación 002) implementada, la plataforma (003) y el inventario de endpoints aprobado (prompt 2).
> **Seguimiento:** [paridad-pantallas.md](paridad-pantallas.md).

## Cómo se usan

- **No se ejecutan los 7 prompts de arranque de una vez.** Son sesiones separadas, con aprobación del usuario entre una y otra.
- Para la migración se repite el **prompt de pantalla** (M1) una vez por grupo de pantallas, en el orden de abajo. Cada ejecución es una sesión.
- Se hace primero el **piloto** (M0): fija los patrones que M1 reutiliza.

**Orden propuesto:** shell y autenticación → datos maestros → compañías → personas con historial y credenciales → usuarios y roles → unidades organizativas → áreas de acceso → permisos → evaluación de acceso. Las cuatro primeras bastan para empezar Comedores.

## M0 — Piloto: shell, inicio de sesión y cambio de contraseña

**Prompt:** Lee `CLAUDE.md`, `docs/decisiones/D-FE-01-frontend-blazor-server.md`, `specs/002-…` y `docs/integracion/paridad-pantallas.md`. Implementa solo `AppShell`, `LoginPage` y `CambiarPasswordPage` en Blazor Server, consumiendo la API por HTTP con el cliente tipado de la especificación 002. Define y documenta los patrones que reutilizarán las demás pantallas: formularios con validación (reutilizando los validadores de la capa de aplicación), mensajes de error a partir de los códigos de error existentes, estado de carga, navegación por módulos contratados, accesibilidad por teclado y pruebas de UI.
**Terminado cuando:** inicio de sesión y cambio de contraseña funcionan; un cambio de alcance o de estado del usuario se aplica a un circuito abierto sin nuevo inicio de sesión; hay pruebas; los patrones están en un documento corto en `docs/`.
**No hacer:** tocar `frontend/`; modificar endpoints o pruebas fuera de lo que indique el inventario aprobado; acceder a la BD o a la capa de aplicación desde los componentes.

## M1 — Prompt de pantalla (se repite por grupo)

**Prompt:** Migra el grupo **[ÁREA]** de `docs/integracion/paridad-pantallas.md`. Pasos: (1) lee las pantallas React del grupo, su `api.ts`, `hooks.ts` y esquemas, y los controladores y servicios de la capa de aplicación que usan; (2) lista el comportamiento observable de cada pantalla (campos, validaciones, estados, errores, confirmaciones, paginación, filtros, teclado) y pídeme confirmar las dudas; (3) implementa los componentes Blazor consumiendo los endpoints que el inventario marca como reutilizar o adaptar, con los patrones de M0 (si falta un endpoint, repórtalo en lugar de saltarlo); (4) escribe pruebas de UI y las pruebas de los endpoints que se adapten; (5) ejecuta las suites existentes y confirma que no se rompió ninguna; (6) actualiza la fila de la tabla de paridad con evidencia.
**Terminado cuando:** cada pantalla del grupo tiene paridad comprobada con una lista de comportamientos contrastada contra React, pruebas en verde y la tabla actualizada.
**No hacer:** retirar React; cambiar reglas de negocio (si encuentras un defecto, repórtalo aparte); ampliar funcionalidad.

## M2 — Verificación de paridad (sesión independiente, antes de retirar)

**Prompt:** Con la sesión limpia, audita el grupo **[ÁREA]** marcado como paridad: compara la lista de comportamientos con ambas implementaciones, ejecuta las pruebas y reporta diferencias. No corrijas: solo informa.
**Terminado cuando:** hay un informe de diferencias y el usuario decide si la fila pasa a *Paridad verificada*.

## M3 — Retiro de React y de lo no reutilizable (solo con todas las filas verificadas)

**Prompt:** Comprueba que todas las filas de `paridad-pantallas.md` están en *Paridad verificada*. Propón el plan para mover a una carpeta **fuera de la solución y del build** (ubicación según Q-30) el frontend React, sus pruebas E2E, y los endpoints y pruebas que el inventario marcó como *retirar*. Antes de mover nada, propón la etiqueta de Git (por ejemplo `etapa1-api-react-final`) y muéstrame cada comando y su efecto.
**Terminado cuando:** el usuario aprueba el plan; el movimiento se hace en un cambio aparte; la solución compila y las suites que quedan pasan.
**No hacer:** borrar en lugar de mover; mover sin etiqueta previa ni aprobación.
