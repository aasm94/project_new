# Estándar de JavaScript ES6+ Moderno

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier aplicación en Node.js, Vainilla JS en Navegador Web, o transpilaciones base hacia V8.
**Ubicación sugerida**: `docs/coding/`

---

## 1. Propósito

Eliminar la sintaxis anticuada de JavaScript y obligar el uso y beneficio de ECMAScript 6 y posteriores. Todo el código debe ser declarativo, libre de `var`, predictivo de scoping, de fácil acceso y seguro funcionalmente.

---

## 2. Variables: Cero tolerancia a "var"

La palabra clave `var` sufre de *Hoisting* que engaña el contexto léxico y contamina el ámbito global (Global Scope o Function Scope). **`var` queda extrictamente prohibido en el ecosistema actual.**

- **`const` por Defecto**: Iniciar TODO instanciamiento o asignación abstracta como `const`. Nos asegura que el puntero (Binding) jamás re-apuntará a otro objeto, evitando reasignaciones estúpidas en código lejano y fortaleciendo el modelo Inmutable.
- **`let` para Mutables**: Usar `let` única y exclusivamente si dicha variable se requerirá como contador (`for`) o sufrirá una recarga intencional y local posterior en esa misma función.

---

## 3. Arrow Functions y Scoping

Para definir funciones anónimas o callbacks asíncronos en componentes funcionales (React) o map/filters:
- Usar siempre **Arrow Functions** (`const foo = () => {}`) en lugar del legado `function(){}`.
- Ventaja técnica Crítica: Las *Arrow Functions* no ligan ni alteran el comportamiento del Objeto `this` original ni lo reasignan en el contexto superior, lo cual evita masivos dolores de cabeza en Event Listeners del frontend o Componentes.

---

## 4. Desestructuración de Objetos y Arrays

- No acceder nunca manualmente hacia propiedades encadenadas delgadas múltiples veces seguidas: `let user = req.body.user; let email = req.body.email;`.
- **Usar desestructuración de sintaxis ES6 directamente**:
```javascript
// Excelente
const { user, email, isActive } = req.body;

// Útil para renombrar de forma limpia o dar default values vacíos a destructuras
const { username: userName, age = 18 } = applicantData;
```

---

## 5. Bucle Funcional Vs Antíguo (Array Methods)

Abandonar para siempre el doloroso loop manual numérico (`for(let i=0; i < arr.length; i++)`) - *a excepción de cuellos fuertes de botella de alto rendimiento algorítmico matemático donde es más rápido.*

Favorecer funciones declarativas inmutables Nativas de Arrays, las cuales explican de forma abstracta su tarea al equipo:
1. **`.map()`**: Cuando quieres devolver un Arreglo Nuevo de exacto el mismo tamaño copiando / modificando los valores del viejo sin alterarlo (Inmutabilidad).
2. **`.filter()`**: Cuando el array que escupimos será menor en longitud porque purgamos los que no pasaron la condición lambda.
3. **`.reduce()`**: Cuando pasamos del Arreglo a un único y consolidado dato (Ej. Retornar la suma final aritmética total, o construir un objeto llave-valor partiendo de listas).
4. **`.find()`**: Para el primer match positivo instantáneo.

---

## 6. Template Strings Literales
Abolir el viejo método de concatenación de Strings sucios (`"Hola " + name + ", bienvenido."`).
- Usar los Backticks (`` ` ``) para inyectar variables en crudo con lectura limpia (Interpolation):
```javascript
const finalMsg = `Hola ${name}, bienvenido al sistema. Tu cobro de ${total} ha procedido.`;
```
