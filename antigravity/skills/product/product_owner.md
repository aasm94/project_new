# Estándar de Product Owner

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Jefes de Producto, Product Managers, Product Owners, Scrum Masters
**Ubicación sugerida**: `docs/management/` o Repositorio de procesos.

---

## 1. Propósito

Definir cómo traducir la estrategia del negocio al equipo de desarrollo, gestionando el Backlog de producto, priorizando tareas y controlando la entrega de valor enfocada a los usuarios.

---

## 2. Gestión del Backlog: El Principio "DEEP"

El Backlog del Producto no es un simple listado de deseos; debe cumplir con el acrónimo **DEEP**:

- **Detailed Appropriately**: Las historias en lo más alto del backlog están detalladas (listas para el sprint), mientras que las de la base son épicas o ideas generales.
- **Emergent**: El backlog cambia constantemente basado en comentarios de mercado, nuevas métricas y hallazgos.
- **Estimated**: Al menos, las tareas prioritarias tienen estimaciones (puntos, horas) del equipo de desarrollo.
- **Prioritized**: Ordenadas estrictamente de acuerdo con el mayor valor de negocio y riesgo.

---

## 3. Definición y Construcción de un MVP

El Producto Mínimo Viable (Minimum Viable Product) debe validar el aprendizaje del negocio con el menor esfuerzo.

### 3.1. Criterios de un buen MVP
No es un producto "incompleto" ni "defectuoso". Es la versión más esbelta de un producto que sigue siendo **Valioso, Útil, Funcional y Fiable** en su segmento central.

### 3.2. ¿Cómo recortar alcance para el MVP?
- Preguntarse: Si esta característica no existiera en la versión 1.0, ¿el usuario aún usaría la aplicación para resolver el problema original? Si es "sí", no va en el MVP.
- Focalizarse en el "Core Flow" y dejar "Nice-to-haves" para la fase de iteración.

---

## 4. Marcos de Priorización

El rol del Product Owner demanda saber decir "No". Usar uno de estos marcos para filtrar ideas:

### 4.1. Análisis MoSCoW
Evaluar features para un Sprint o Release:
- **Must Have**: Crítico; sin esto se cae el lanzamiento o falla la ley/seguridad.
- **Should Have**: Importante, pero un workaround es aceptable temporalmente.
- **Could Have**: Deseable ("Nice-to-have"), mejora UX, si hay tiempo.
- **Won't Have**: Acordado que NO va en este ciclo temporal.

### 4.2. Matriz Impacto vs. Esfuerzo / ICE / RICE
RICE (Reach, Impact, Confidence, Effort) para decisiones granulares:
- **Prioridad Primaria (Quick Wins)**: Alto Impacto / Bajo Esfuerzo.
- **Poryectos Mayores**: Alto Impacto / Alto Esfuerzo.
- **Rellenos (Fill-in)**: Bajo Impacto / Bajo Esfuerzo.
- **Evitar / Descartar**: Bajo Impacto / Alto Esfuerzo.

---

## 5. Análisis de Impacto y Métricas

La toma de decisiones del PO debe basarse en datos.

### 5.1. Definición del Éxito
Por cada Épica o Historia importante, definir cómo sabemos que funcionó:
- *Ej.* Si lanzamos un proceso de pago, el éxito es: "Disminución del 20% en abandonos del carrito".

### 5.2. Métricas Pirata (AARRR)
- **Adquisición**: De dónde vienen los usuarios.
- **Activación**: ¿Tienen una buena primera experiencia?
- **Retención**: ¿Regresan a nuestra plataforma?
- **Recomendación (Referral)**: ¿Nos comparten?
- **Revenue (Ingresos)**: ¿Se puede generar monetización?

---

## 6. Ceremonias, Refinamiento y Descubrimiento

- **Refinamiento (Backlog Grooming)**: El PO se sienta semanalmente o continuamente con el Tech Lead o equipo, revisando requerimientos futuros, resolviendo ambigüedades.
- **Roadmap Mapping**: Compartir la visión a corto (3 meses), mediano (6 meses) y largo plazo, alineando OKRs o KPIs corporativos con las salidas de Software.
