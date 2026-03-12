# Estándar de Comentarios y Documentación en Código

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Código fuente en todo Stack.
**Ubicación sugerida**: `docs/coding/`

---

## 1. Propósito

Evitar la contaminación del código base con comentarios innecesarios, redundantes, código muerto comentado, u obsoletos, y promover únicamente dos tipos de documentación útil: Explicación del "Por qué" (Decision Records en el código) y la indexación estructurada funcional (ej. JSDoc/Docstrings).

---

## 2. La Regla del 'QUÉ vs POR QUÉ'

- **NO comentes QUÉ estás haciendo**. El código en sí se encarga de demostrar *qué* hace (con una correcta separación de funciones y nombrado de variables).
- **SÍ comenta POR QUÉ lo estás haciendo**. Específicamente, cuando las reglas de negocio sean bizarras, existan limitantes arquitectónicas o se tenga que hacer un "Workaround / Hack" obligatoriamente temporal para salvar el día.

### Ejemplo
- *Mal (Redundante)*:
  ```javascript
  // Obtenemos al usuario por su ID
  const user = await getUserById(12);
  
  // Si el usuario es mayor de edad
  if (user.age >= 18) { ... }
  ```

- *Bien (El Por Qué de negocio)*:
  ```javascript
  const user = await getUserById(12);

  // Por ley tributaria (Art. 142B), los usuarios de México no pueden transar
  // en la plataforma si su registro supera la edad de 90 años en este endpoint en especifico.
  // TODO: Refactorizar esto al módulo de compliance el proximo Sprint.
  if (user.age >= 18 && user.age <= 90) { ... }
  ```

---

## 3. JSDoc / DocStrings Estructurados

La única excepción donde se comenta el comportamiento o el "Qué" (y los parámetros) es cuando se declaran servicios base, métodos públicos re-usables por todo el equipo, o se define una Interfaz donde ni siquiera existe el tipado fuerte.

En TypeScript/Javascript, utilícese **JSDoc**.
En Python, utilícese **Docstrings (PEP 257)**.

- Debe colocarse inmediatamente arriba de la abstracción de la Función/Clase.
- Ayuda mágicamente a que Visual Studio Code, herramientas estáticas de linteo o Inteligencias Artificiales lean el IntelliSense en otras pestañas.

*Ejemplo JSDoc*:
```javascript
/**
 * Calcula el impuesto compuesto mensual de una deuda base.
 * ¡Atención! Esta función es costosa a nivel de CPU, correr asíncronamente
 * cuando la matriz supere los 100K registros.
 *
 * @param {number} principal - El monto sin intereses inicial.
 * @param {number} months - Los meses expirados de la deuda.
 * @param {string} [country='MX'] - ISO del país para calcular tablas de la base local. Opcional.
 * @returns {number} El subtotal con recargo finalizado.
 * @throws {ValidationError} Si el factor de meses excede los límites legales.
 */
function compoundTax(principal, months, country = 'MX') { ... }
```

---

## 4. Comentarios Prohibidos

Los siguientes comentarios deben ser borrados sistemáticamente en cada Pull Request:

1. **Código Zombi Comentado**: Bloques enteros de funciones viejas con la excusa "Por si acaso lo llegamos a ocupar o falla la nueva versión". Git se encarga de guardar todo el historial por siempre. Bórralo.
2. **Firmas y Trazas Manuales**: "Modificado por @charly el 14-Oct-2023 para resolver TKT-192". Eso le pertenece al historial de *Git Blame / Git Log*, no al cuerpo del archivo.
3. **Comentarios de Barrera Gigantes**:
   ```javascript
   // ==========================================
   // ============== VARIABLES =================
   // ==========================================
   ```
   *Si tu archivo es tan grande que requiere banners de separación ASCII, tu archivo hace demasiadas cosas y viola el principio de Única Responsabilidad de SOLID. Divídelo.*

---

## 5. Etiquetas de Tarea Rápida (TODOs y FIXMEs)

Usar anotaciones mayúsculas en comentarios si y sólo si un trabajo queda realmente pendiente para un Refactor rápido.
- `// TODO: [Nombre/Ticket]` Indica una deuda técnica planeada.
- `// FIXME: [Contexto]` Hay un bug conocido pero de baja intensidad o *hack* que se pasó a Prod pero que hay que atender antes del siguiente Quarter.
