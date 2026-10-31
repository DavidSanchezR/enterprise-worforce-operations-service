# Línea base verificada — estado real del repositorio (2026-10-30)

> **Consultar cuando:** se necesita saber en qué estado real quedó la solución antes de empezar la etapa de Comedores (Git, compilación, pruebas, pantallas).
> **Certeza:** verificado por ejecución el 2026-10-30, sin modificar código, sin instalar dependencias globales y sin borrar nada. Lo marcado como *inferido* no está confirmado. El detalle completo está en la sección 8 de [informe-brechas-eac-vs-comedores.md](informe-brechas-eac-vs-comedores.md).
> **Entorno:** SDK `dotnet` 10.0.403; Docker Desktop 29.9.2 (motor Linux).

## Resumen

**La solución no compila tal como está en el repositorio.** El código no tiene errores; falla la auditoría de paquetes NuGet. Todas las pruebas pasan cuando ese aviso se rebaja solo para la prueba.

## Git

- Rama `main`, al día con `origin/main` y sin cambios pendientes.
- Hay un único commit, `efc106f` "chore: linea base inicial (EAC Etapa 1 y documentacion de contexto)".
- **No hay ninguna etiqueta.** CLAUDE.md exige una antes de mover o retirar código, así que habrá que crearla primero.

## Compilación

- `dotnet build backend/EnterpriseAccessControl.slnx` **falla con 8 errores** (NU1902 y NU1903). El paquete `System.Security.Cryptography.Pkcs` 9.0.11 tiene vulnerabilidades conocidas: una moderada (GHSA-2fmv-rv2c-59x7) y una alta (GHSA-846f-f764-76mx). Como el proyecto trata las advertencias como errores (`TreatWarningsAsErrors`), la compilación se detiene en la restauración, antes de empezar a compilar. Afecta a Infrastructure, Api, IntegrationTests y ContractTests.
- Ese paquete no se declara en ningún proyecto; lo arrastra otro. El origen probable es `Microsoft.EntityFrameworkCore.SqlServer` 10.0.12 a través de `Microsoft.Data.SqlClient` (*inferido*, no confirmado).
- Para medir el código, se repitió la compilación con `-p:WarningsNotAsErrors=NU1902%3BNU1903`, que rebaja esos dos avisos solo en esa ejecución. Así compilan los 7 proyectos sin ningún error; quedan 16 advertencias, todas NU1902 o NU1903.

## Pruebas

Ejecutadas con `dotnet test --no-build` sobre esa compilación de diagnóstico.

| Suite | Resultado |
|---|---|
| Unidad | 155 de 155 pasan |
| Integración (SQL Server 2022 en contenedor, Testcontainers) | 593 de 593 pasan (1 min 5 s) |
| Contrato | 252 de 252 pasan (el informe de brechas decía 246) |

## Pantallas del frontend React

- Hay 11 pantallas reales en 10 carpetas de `frontend/src/features`: login, cambio de contraseña, usuarios, compañías, unidades organizativas, áreas de acceso, permisos, evaluación de acceso, maestros, personas e historial de persona. La ruta `/` (Dashboard) es un marcador `PantallaPendiente`, no una pantalla real.
- La relación de cada pantalla con sus componentes y pruebas está en la tabla 8.3 del [informe de brechas](informe-brechas-eac-vs-comedores.md).
- Hay 5 especificaciones E2E (Playwright) con 9 pruebas. Dos de ellas, `cs009-quickstart` y `multi-principal-quickstart`, recorren el flujo por la API, sin navegar pantallas.
- **No tienen ninguna prueba E2E** siete pantallas: cambio de contraseña, unidades organizativas, áreas de acceso, permisos, evaluación de acceso, maestros y la lista de personas.
- Las pruebas de Vitest (unos 199 casos en 20 archivos) y de Playwright solo se contaron por texto; **no se ejecutaron**.

## Correcciones aplicadas al informe de brechas

- Pruebas de contrato: 252, no 246.
- Migraciones: 13, no 12 (12 de esquema y 1 de datos semilla, `SeedMaestrosPeru`).
- `CLAUDE.md` y `docs/README.md` ya existen; el informe los daba por ausentes.

Se comprobó que siguen siendo correctas estas cifras: los 72 endpoints (atributos `[Http*]` en 10 controladores), los 6 triggers de no solapamiento, la constitución 1.1.1, los 2 roles administrativos y la falta de integración continua.

## Pendiente de decisión

**Cómo arreglar la compilación (no aplicado).** Opciones:

- fijar una versión corregida de `System.Security.Cryptography.Pkcs`;
- actualizar EF Core o SqlClient.

No se recomienda silenciar el aviso. Hasta que se corrija, nada nuevo compilará con la configuración del repositorio.
