# Estándar de Flujo Git (Git Workflow)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier repositorio de versiones de código (GitHub, GitLab, Bitbucket).
**Ubicación sugerida**: `docs/protocols/` o en el root `CONTRIBUTING.md`.

---

## 1. Propósito

Estandarizar el trabajo de equipo, asegurar un historial limpio y legible para sistemas de integración continua (CI/CD) y proveer herramientas precisas para generación automática del Changelog mediante mensajes estructurados.

---

## 2. Estrategia de Ramas (Trunk-based / Feature Branching)

Se priorizará una topología similar a GitHub Flow para despliegues ágiles, adaptada a tres ambientes:

- `main` / `master` (Producción): Rama inmaculada. Coincide exactamente con lo que el cliente final experimenta. Nunca se comitea directamente aquí.
- `staging` o `develop` (Pruebas / QA): Entorno central donde se integran las características terminadas para pruebas end-to-end antes de salir del ciclo (si la escala del equipo lo requiere).
- `feature/*`, `bugfix/*`, `hotfix/*`, `chore/*` (Ramas de Tarea): Donde el trabajo real sucede.

### 2.1 Reglas del Branching
Toda rama debe tener un prefijo lógico más el ID de la historia/ticket, seguido de una descripción muy corta en formato `kebab-case`.
- *Ejemplo*: `feature/US-014-export-pdf`
- *Ejemplo*: `bugfix/TKT-04-login-crash`
- *Ejemplo*: `hotfix/DB-timeout-prod` (Los hotfixes salen desde `main` y regresan directamente a `main`, portándose luego sobre las de QA a la inversa).

---

## 3. Mensajes de Commit (Conventional Commits)

Es **obligatorio** el uso de Conventional Commits. Todo mensaje debe comunicar clara y estructuradamente su intención para que el CI/CD pueda calcular un incremento de versión (SemVer `v1.x.x`).

### 3.1 Estructura
```
<tipo>(<alcance opcional>): <descripción corta>

[cuerpo opcional detallando el porqué de los cambios]

[pie opcional con referencia a tickets, ej. Closes #123]
```

### 3.2 Tipos Permitidos
- `feat`: Una característica completamente nueva. *(Dispara un MINOR bump)*.
- `fix`: Corrección de un error o bug. *(Dispara un PATCH bump)*.
- `chore`: Modificación de herramientas, configuración de webpack, actualización del npm, ignorar archivos, CI/CD.
- `refactor`: Refactorización de código que ni arregla un bug ni añade característica nueva.
- `docs`: Cambios centrados únicamente en directorios como `/docs` o READMEs.
- `test`: Añadido o modificación de pruebas o tests.
- `style`: Estilos y corrección de linting (espacios, saltos de línea, comillas).

*Ejemplos Válidos:*
`feat(auth): add google sign-in passport strategy`
`fix(cart): prevent double charge on multiple clicks`
`chore: update github actions node version to 20`

---

## 4. Pull Requests (PR) / Merge Requests (MR)

Un PR es la unidad de revisión del equipo.

- **Tamaño Mínimo**: Un PR no debería tomar más de 15 minutos en revisarse. Si el PR toca 35 archivos, debe ser partido.
- **Formato del PR**:
  - ¿Qué hace este PR? (Enlazar Ticket / US)
  - Pasos para que el reviewer pueda probar en su máquina el feature propuesto.
  - Screenshots si hay alteración gráfica Front-end.
  - Checklist del "Definition of Done" (Tests pasan, Build sano).
- **Squash and Merge**: Al integrarse una Feature Branch al entorno Principal (main), siempre aplicar configuración de rebase/Squash para empaquetar 15 micro-commits desordenados en 1 solo commit atómico con el título y el Conventional Commit limpio.
