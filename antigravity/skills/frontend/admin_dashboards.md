# Estándar de Dashboards Administrativos

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Paneles de control, portales de administración B2B, backoffices, CRM o ERPs internos.
**Ubicación sugerida**: `docs/design/` o `docs/frontend/`

---

## 1. Propósito

Establecer reglas pragmáticas orientadas a sistemas de gran escala de datos, donde la prioridad absoluta sobre la UI es la operabilidad, legibilidad, filtros veloces y la correcta representación de tablas complejas para usuarios recurrentes de tipo "heavy-user".

---

## 2. Diseño de Tablas (Data Tables)

El 80% de los Dashboards Administrativos son tablas con datos vivos. El rendimiento visual y técnico aquí es crítico.

### 2.1 Paginación vs Scroll Infinito
- **Tablas B2B (Backoffice)**: Priorizar fuertemente Paginación Offset Clásica (Página 1 de 54) o Paginación de Cursores con botones ("Anterior", "Siguiente"). Permite a recursos financieros/logísticos tener una URL confiable.
- **Scroll Infinito**: Evitarlo a nivel de Dashboard Core. Solo para feeds estilo "Timelines".

### 2.2 Legibilidad
- **Alineación**: Textos a la izquierda (identificadores, emails). Datos Numéricos Financieros y Fechas estrictamente alineados a la **derecha**, esto permite comparar dimensionalmente magnitudes.
- **Sticky Headers**: Todo Data Table cuyo contenido exceda el alto de la ventana visible debe fijar permanentemente su cabecera (`position: sticky`) al hacer scroll.
- **Botones de Acción Ocultos**: Usar menús contextuales ("Tres puntos") al final de cada fila si existen más de 2 acciones posibles, en lugar de saturar de botones visuales (*"Ver", "Editar", "Borrar", "Duplicar"*).

---

## 3. Comportamiento en Filtros y Búsqueda de Datos

Los filtros no deben ser una carga cognitiva ni bloquear el flujo.

### 3.1 Url Sincronizada (Source of Truth)
En lo posible, TODO filtro aplicado, parámetro de búsqueda general o cambio de paginación debe reflejarse sincrónicamente en el *Query String* de la URL (`?status=active&page=2&search=acme`).
- Esto permite que un usuario comparta un link exactamente en la misma vista de reporte filtrada hacia otro miembro del equipo.
- Al recargar la página, se recupera el mismo estado.

### 3.2 Debounce de Petición
Todo input de búsqueda textual (Searchbars) que ejecute peticiones de API al tipeo debe estar envuelto bajo una función `debounce` de entre 300ms y 500ms para evitar ahogar el servicio del backend.

---

## 4. Visualización de Datos (Gráficos)

- **Menos es más**: Si una métrica crítica puede ser leída eficientemente solo como un número grande con un indicador delta en texto plano, es preferible a poner un inmenso gráfico pastel. Es la vista por defecto de un *KPI Card*.
- **Temporalidad Constante**: Todo reporte gráfico debe tener controles de tiempo visibles (Hoy, Últimos 7 Días, Mes, YTD). Nunca obligar al usuario a adivinar la temporalidad subyacente de un Chart.
- Ejes (Axis) siempre rotulados o interpretables según contexto general.

---

## 5. Diseño Agresivamente Útil ("Action-Oriented")

- Las vistas primarias del dashboard deberían contestar "¿Qué debo arreglar o atender hoy?".
- Presentar vistas filtradas por omisión hacia elementos pendientes (ej. "Facturas Impagas" por sobre "Todas Las Facturas Históricas").
- Uso de colores semánticos restrictivo para la operación rápida: 
  - Rojo/Gris oscuro: Sólo algo falló o bloquea sistema.
  - Verde: Exitoso.
  - Azul/Primario: Elemento interactable no-destructivo.
