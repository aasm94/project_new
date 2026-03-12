# Estándar de Clean Code y Principios de Diseño

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier lenguaje de programación (Python, TS, PHP, Go, etc).
**Ubicación sugerida**: `docs/coding/` o `docs/architecture/`

---

## 1. Propósito

Garantizar que el código escrito hoy pueda ser leído, entendido y modificado por otro desarrollador (o por uno mismo) en seis meses, sin introducir fragilidad al sistema.

---

## 2. Nomenclatura (Naming Conventions)

El nombre de una variable, función o clase debe responder a 3 preguntas: *¿Por qué existe?*, *¿Qué hace?*, y *¿Cómo se usa?*

- **Descriptivo frente a corto**: Preferir `elapsedTimeInDays` o `daysSinceModification` sobre `d` o `t1`.
- **Funciones (Verbos)**: Las funciones hacen cosas. Deben empezar por un verbo fuerte (`calculateTotal`, `fetchUserData`, `isUserActive`). Nunca llamarlas por sustantivos estáticos.
- **Clases (Sustantivos)**: Representan conceptos. Deben ser sustantivos o frases sustantivadas (`UserAccount`, `PaymentProcessor`). Evitar palabras genéricas vacías como `Manager`, `Processor`, `Data` o `Info` a menos que sean muy específicas.
- **Booleanos**: Siempre hacer que suenen como una pregunta de Verdadero o Falso añadiendo prefijos como `is`, `has`, `can`, `should` (ej. `isActive`, `hasPermission`).

---

## 3. Funciones y Métodos

### 3.1. Regla de Tamaño y Responsabilidad Única
- Las funciones deben ser pequeñas. Idealmente no más de 15-20 líneas de código efectivo.
- **Hacer una sola cosa**: Si la función hace más de un trabajo funcionalmente (ej. `validateHeadersAndInsertDatabaseAndEmailUser()`), **debe separarse en 3 funciones**.

### 3.2. Parámetros (Argumentos)
- Una función no debe tener más de 3 parámetros.
- Si requiere 4 o más parámetros, es muy probable que esos parámetros constituyan un concepto agrupado. En ese caso, envolverlos en un Objeto o Data Class (`DTO`, interface o estruct).

---

## 4. Comentarios

**"Un comentario es un síntoma de nuestro fracaso para expresarnos en el código."** - *Robert C. Martin*

- **El código se explica a sí mismo**: No explicar **QUÉ** hace el código (eso lo explica nombrar bien la función).
- **Justificar el POR QUÉ**: Únicamente escribir comentarios cuando hay una decisión de negocio oscura, un *workaround* extraño producto de un bug externo, o un algoritmo complejo que tiene justificación matemática inusual.
- Borrar el código comentado. Jamás dejar bloques gigantes de código comentado inerte "por si acaso"; para eso existe Git.

---

## 5. Principios SOLID (Cheatsheet Práctico)

1. **(S) Single Responsibility (Responsabilidad Única)**
   - Un módulo, clase o función debe tener un **único motivo para cambiar**.
   - *Mal*: La clase `Employee` calcula pagos del SAT, guarda en BB.DD, e imprime HTML.
2. **(O) Open/Closed (Abierto/Cerrado)**
   - El código debe estar abierto para extenderse pero cerrado para modificación. Usar Polimorfismo / Interfaces en lugar de añadir if/else para cada tipo nuevo.
3. **(L) Liskov Substitution (Sustitución de Liskov)**
   - Si Clase B hereda de Clase A, deberías poder usar Clase B en donde esperabas Clase A sin que el sistema colapse.
4. **(I) Interface Segregation (Segregación de Interfaces)**
   - Es mejor tener muchas interfaces pequeñas y específicas (`IEmailSender`, `ISmsSender`) que una sola gigante monstruosa (`iNotifier`) que fuerce a clases a implementar métodos nulos.
5. **(D) Dependency Inversion (Inversión de Dependencias)**
   - Los módulos de alto nivel (Lógica central) no deben depender de los de bajo nivel (Detalles como MySQL, Librería Stripe). Ambos deben depender de **Abstracciones (Interfaces)**. Recibir la base de datos por inyección al constructor, no instanciarla adentro.
