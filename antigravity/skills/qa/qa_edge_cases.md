# Estándar de Quality Assurance y Edge Cases

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Equipos de QA, pruebas manuales, automatizadas (E2E) y seguridad del producto.
**Ubicación sugerida**: `docs/qa/`

---

## 1. Propósito

Dotar al tester o desarrollador evaluador de un manual para buscar vulnerabilidades lógicas, validación de fallos en interfaz, corrupción de flujos asíncronos en componentes y mitigación de errores de usuario antes de un lanzamiento sobre ramas Release o Staging.

---

## 2. Los Flujos de "Sad Path" o Caso Límite

A diferencia del flujo feliz, un QA debe pensar sistemáticamente "Qué pasa si el usuario..."

### 2.1 Concurrencia e Impaciencia (Double Spend / Double Submit)
- ¿Qué pasa si el usuario presiona "Pagar", "Crear" o "Enviar" 5 veces casi simultáneamente en su móvil de forma exasperada porque su red es lenta?
  - *Expectativa a QA*: El Frontend deshabilita el botón inmediato de la primera petición y bloquea. El Backend debió atrapar el mismo payload por protección contra dobles transacciones (Idempotencia o Throttle).

### 2.2 Red Intermitente ("Modo Tren/Túnel")
- ¿Qué pasa si el equipo carga el formulario online, el cliente se mete a un túnel, pierde internet por 40 segundos e intenta darle a guardar?
  - *Expectativa a QA*: El frontend alerta "Sin Conexión" sutilmente, no se paraliza para siempre en "Spinner de Carga Infinito".

### 2.3 Expiración de Sesión (Timeouts)
- ¿Qué ocurre si un usuario abrió una pestaña, se fue a comer, regresa tras 3 horas (cuyo token backend JWT expiró a 1 hora) y trata de modificar datos?
  - *Expectativa a QA*: El backend revienta el request pero el Frontend es capaz de atrapar limpiamente el estatus `401 Unauthorized` obligando al login amistosamente, sin dejar pantallas en blanco (White Screen of Death).

---

## 3. Entradas Extremas Destructivas (Boundary QA Input)

Todo formulario o endpoint debe ser retado destructivamente.

1. **Vaciado puro**: Mandar nulos, indefinidos, diccionarios en blanco, array vacíos en vez de texto normal.
2. **Desbordamiento**: Enviar cadenas de texto de 5,000 + caracteres (Párrafos "Lorem Ipsum" masivos) en inputs de primer y segundo nombre; retar al varchar de la base de datos para asegurar el corte.
3. **Escapes Ocultos / XSS**: Usar cadenas provocativas de scripts HTML (`<script>alert(1)</script>`) en cajones de comentarios para asegurar que la vista la renderiza desarmada (sanitize).
4. **Fechas Incongruentes**: Poner una Fecha de Inicio *mayor* a la Fecha de Fin para los reportes; o Fecha de Nacimiento fijada en 1845. El sistema debe validar la paradoja temporal.

---

## 4. Cross-browser y Dispositivos Críticos

Al ser Agnóstico, un sistema Web/Movil debe probarse bajo ambientes críticos que no simulan los Devs.

- **Frontend Safari (iOS)**: Todo lo que brilla en Chrome falla ocultamente en el Safari de iOS debido a reglas como Height dinámicos complejos (`100vh`) y renderizado de campos nativos. TODO proyecto que sea B2C o masivo al cliente final requiere el Double-Check manual en un dispositivo iOS físico por parte del tester.
- **Teclado Virtual Interpuesto**: Comprobar formularios abriéndolos con un móvil de pantalla pequeña y observando sus botones "Siguientes / Salvar" al alzar el teclado Android/iOS de la mitad de la pantalla hacia arriba.

---

## 5. Reporte de Bugs Eficaz

Todo Bug detectado por QA e ingresado a (Jira / Linear) DEBE cumplir la sagrada trinidad:

1. **Pasos para Reproducir (Steps to Reproduce)**: Numéricos, muy detallados, listando exactamente dónde se da click en qué orden.
2. **Resultado Observado**: Qué está pasando. "El botón se queda trabado y explota console log Pánico."
3. **Resultado Esperado**: "Debería regresar al Dashboard".
4. **Entorno/Contexto (Browser/OS)**: Versión Chrome, Safari, etc.
5. **Video / Payload Gráfico**: Obligatorio siempre incluir grabación de pantalla visual.
