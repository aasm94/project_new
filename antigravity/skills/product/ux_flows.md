# Estándar de UX Flows

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier aplicación (Web, Móvil, Desktop, Agnostic)
**Ubicación sugerida**: `docs/design/` o `docs/ux/`

---

## 1. Propósito

Definir los flujos de navegación, los estados de pantalla y la lógica de interacción del usuario. El objetivo es asegurar que la experiencia sea intuitiva, consistente y que contemple todos los escenarios posibles (felices y alternativos) antes de iniciar el desarrollo técnico.

---

## 2. Entregables y Artefactos

Todo flujo de UX debe documentarse mediante los siguientes artefactos conceptuales o visuales:

- **Mapa de Sitio / Arquitectura de Información**: Jerarquía de las pantallas y módulos.
- **Diagramas de Flujo (User Journeys)**: Secuencia paso a paso que sigue el usuario para completar una tarea.
- **Wireframes / Mockups**: Representación visual (puede ser de baja o alta fidelidad).

*Nota: La herramienta es agnóstica (Miro, Figma, Markdown Mermaid, Excalidraw), lo importante es la claridad lógica.*

---

## 3. Estados de Pantalla

Cada pantalla o componente clave debe definir explícitamente cómo se comporta en los siguientes 5 estados:

1. **Estado Ideal (Blank State)**: La vista predeterminada cuando la data carga correctamente y está poblada.
2. **Estado Vacío (Empty State)**: Qué ve el usuario cuando no hay datos (ej. "No tienes mensajes nuevos"). Debe incluir un Call to Action (CTA) para orientar al usuario.
3. **Estado de Carga (Loading/Skeleton)**: Retroalimentación visual mientras el sistema procesa una petición (spinners, skeletons).
4. **Estado de Error**: Qué sucede cuando falla una petición, se cae la red o el input es inválido.
5. **Estado Parcial**: Qué ocurre si solo carga una parte de la información o los datos están incompletos.

---

## 4. Lógica de Usuario y Reglas de Negocio

Para cada interacción principal (ej. "Enviar formulario", "Pagar", "Registrarse"), se debe documentar la lógica:

### 4.1. Caso de Éxito (Happy Path)
El flujo sin fricciones, donde el usuario hace todo correctamente y el sistema responde idealmente.

### 4.2. Casos Alternativos
Caminos diferentes para llegar al mismo objetivo (ej. "Inicia sesión con Google" vs "Inicia sesión con email").

### 4.3. Casos de Borde (Edge Cases)
Escenarios inusuales pero posibles y cómo manejarlos de forma segura:
- El usuario hace doble clic en el botón de pago.
- El usuario refresca la página a mitad del proceso.
- La sesión expira mientras el usuario llenaba datos.

---

## 5. Prácticas de Handoff (Entrega a Desarrollo)

- **Nomenclatura Consistente**: Nombrar las pantallas lógicamente (ej. `AUTH-01-Login`, `DASH-01-Overview`).
- **Anotaciones**: Especificar animaciones, tiempos de transición o validaciones de frontend (ej. "El botón se habilita solo si el email es válido").
- **Trazabilidad**: Relacionar el flujo de UX con la Historia de Usuario correspondiente.
