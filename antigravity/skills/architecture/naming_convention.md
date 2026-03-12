# Estándar de Nomenclatura (Naming Conventions)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Código fuente, bases de datos y variables en cualquier lenguaje.
**Ubicación sugerida**: `docs/coding/`

---

## 1. Propósito

Lograr que el código sea predecible, auto-documentado y legible tanto para desarrolladores humanos como para agentes basados en Inteligencia Artificial. Una buena nomenclatura elimina en gran medida la necesidad de escribir comentarios adicionales.

---

## 2. Reglas de Legibilidad

"Tu código lo vas a leer 10 veces más de las que lo vas a escribir".

- **Nombres Expresivos**: No usar iniciales ni acrónimos inventados.
  - *Mal*: `let p = new P();` o `usrAuthTkn = "xyz";`
  - *Bien*: `let payment = new Payment();` o `userAuthToken = "xyz";`
- **Pronunciables y Buscables**: Un nombre debe ser pronunciable en voz alta. Si no puedes decirlo, tampoco podrás buscarlo rápidamente con `Ctrl + F`.

---

## 3. Convenciones por Tipo de Dato / Estructura

### 3.1. Booleanos (Preguntas)
Las variables que guardan `true` o `false` deben leerse como una pregunta.
- Añadir prefijos: `is`, `has`, `can`, `should`.
- *Mal*: `login = true;`, `error = false;`
- *Bien*: `isUserLoggedIn = true;`, `hasError = false;`, `canEditProfile = true;`

### 3.2. Arreglos y Colecciones (Plurales)
Toda lista, matriz o arreglo de objetos MÚLTIPLES debe ir en Plural.
- *Mal*: `let user = ["Juan", "María"];`
- *Bien*: `let users = ["Juan", "María"];` o `let userList = [...]`

### 3.3. Funciones (Verbos + Sustantivo)
Las funciones y métodos ejecutan acciones. Deben nombrarse con un verbo Fuerte que de cuenta de su trabajo, seguido de la entidad que afectan.
- *Mal*: `function data()` o `function report()`
- *Bien*: `function fetchUserData()`, `function generateReport()`, `function processPayment()`

### 3.4. Clases y Modelos (Sustantivos, PascalCase)
Representan entidades vivas en el código.
- *Mal*: `class createInvoice` o `class invoiceManager`
- *Bien*: `class InvoiceBuilder`, `class UserAccount`

---

## 4. Convenciones de Casing (Carcasa) por Ecosistema

Es fundamental mantener consistencia sobre el estándar de facto de cada lenguaje:

- **camelCase**: Usado primordialmente en JS/TS, Java y C# para `variables` y `funciones`. (ej. `userProfileName`).
- **snake_case**: Usado primordialmente en Python, PHP, Ruby y **Bases de Datos SQL** para `variables`, `DB Columns` y `funciones`. (ej. `user_profile_name`).
- **PascalCase**: Usado universalmente para `Clases` e `Interfaces`. (ej. `UserProfile`).
- **SCREAMING_SNAKE_CASE**: Usado universalmente para variables pre-fijadas, `Constantes Globales` y variables de Entorno (.env). (ej. `MAX_UPLOAD_SIZE = 5`).

---

## 5. El Contexto Importa

No es necesario repetir el nombre de la clase dentro de sus propios métodos o variables internas.
- *Mal*:
```typescript
class Car {
  public carColor: string;
  public startCarEngine(): void {}
}
```

- *Bien*:
```typescript
class Car {
  public color: string;
  public startEngine(): void {}
}
```
*(Al leerlo, simplemente llamarás a `myCar.color`, lo que es perfectamente claro).*
