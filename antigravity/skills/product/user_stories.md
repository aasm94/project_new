# Estándar de Historias de Usuario

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier metodología Ágil o marco de trabajo (Scrum, Kanban, etc.)
**Ubicación sugerida**: `docs/user-stories/` o herramienta de ticket (Jira, Trello, Linear)

---

## 1. Propósito

Transformar ideas, requerimientos o descubrimientos operativos en unidades de trabajo entendibles por el negocio y por los desarrolladores. La Historia de Usuario (US) se centra en el "qué" y el "por qué", delegando el "cómo" a tareas técnicas (WBS o Sub-tasks).

---

## 2. Anatomía Principal

Toda definición de una historia de usuario debe seguir la narrativa estándar enfocada en el valor.

### 2.1 La Narrativa

> **Como** [Usuario / Rol específico]
> **Quiero** [Acción concisa / Característica]
> **Para** [Beneficio medible o meta del negocio]

- **Correcto:** Como cliente frecuente, quiero guardar mis datos de tarjeta de crédito, para pagar más rápido en compras futuras.
- **Incorrecto:** Como sistema, quiero una tabla en PostgreSQL para guardar los hash de contraseñas. *(Eso es una tarea de desarrollo, no una User Story)*

---

## 3. Criterios de Aceptación (Acceptance Criteria)

Condiciones que deben cumplirse para dar por terminada y aceptada la historia. Se recomienda el formato BDD (Behavior-Driven Development):

- **Dado que** (Given): El contexto inicial o condición preexistente.
- **Cuando** (When): La acción o el evento disparador.
- **Entonces** (Then): El resultado esperado.

**Ejemplo:**
- *Dado que* estoy en la pantalla de "Carrito" y tengo 1 producto.
- *Cuando* presiono "Eliminar producto".
- *Entonces* el producto desaparece, el subtotal se actualiza a $0, y veo el mensaje "Tu carrito está vacío".

---

## 4. Atributos Complementarios

Para gestionar correctamente el Backlog, una US debe incluir (como mínimo):

| Atributo | Descripción |
|----------|-------------|
| **ID** | Identificador único (ej. `US-014`). |
| **Título** | Corto, claro y descriptivo (máximo 5-7 palabras). |
| **Estado** | Ej. `Backlog` (Por definir) -> `To Do` -> `In Progress` -> `QA` -> `Done`. |
| **Prioridad** | Rango (Alta, Media, Baja) o nivel de urgencia/impacto. |
| **Puntos / Estimación** | Nivel de complejidad (Fibonacci, Tallas de Camiseta (S,M,L), horas, etc.). |
| **Dependencias** | Qué requiere completarse antes de iniciar esta historia. |

---

## 5. Definition of Ready (DoR)

Checklist genérico para saber si una US está lista para entrar a desarrollo:
- [ ] La narrativa (*Como/Quiero/Para*) está completa.
- [ ] Los Criterios de Aceptación están definidos y sin ambigüedad.
- [ ] Los diseños / Mockups requeridos están adjuntos (si hay impacto visual).
- [ ] La historia ha sido estimada en esfuerzo por el equipo técnico.
- [ ] Sus dependencias han sido resueltas.

---

## 6. Definition of Done (DoD)

Checklist genérico para dar por cerrada/terminada la US:
- [ ] El código ha sido revisado (Pull Request aprobado).
- [ ] Pasa las pruebas automatizadas y en entorno de Staging.
- [ ] Cumple con todos los Criterios de Aceptación.
- [ ] Fue validado por QA o el Product Owner.
- [ ] Documentación / Release notes actualizadas.
