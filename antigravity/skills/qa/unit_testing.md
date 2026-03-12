# Estándar de Pruebas Unitarias (Unit Testing)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier suite de pruebas automatizadas (Ej. Jest, Vitest, Pytest, PHPUnit).
**Ubicación sugerida**: `docs/testing/` o `docs/qa/`

---

## 1. Propósito

Definir lineamientos estandarizados para escribir pruebas unitarias que verifiquen piezas pequeñas, inyectando confianza constante antes del despliegue en ambientes remotos. Las pruebas unitarias jamás deben golpear bases de datos en vivo, ni infraestructuras de terceros.

---

## 2. Anatomía de la Prueba (Patrón AAA / Arrange-Act-Assert)

Toda prueba automatizada individual debe divirse visualmente mediante espacios en blanco o comentarios en tres actos puros.

1. **Arrange (Preparar)**: Setup del estado inicial simulado, creación de variables *mock*, *stubs*, inicialización de las clases y sus dependencias (inyecciones).
2. **Act (Actuar)**: La llamada única a la función/método o re-renderizado del componente (el comportamiento que queremos testear).
3. **Assert (Afirmar)**: Verificación del resultado que regresó el "Act" frente a la expectativa generada en el "Arrange".

*Ejemplo clásico*:
```typescript
it('debería calcular el total correctamente aplicando el 10% de impuesto', () => {
  // 1. Arrange
  const calculateTotal = new PaymentCalculator();
  const rawSubtotal = 100;
  
  // 2. Act
  const finalPrice = calculateTotal.withTaxes(rawSubtotal);
  
  // 3. Assert
  expect(finalPrice).toBe(110);
});
```

---

## 3. Evitar Pruebas de Implementación

**"Prueba el Comportamiento, no la Implementación".**

Si un Test falla cada vez que cambias el nombre de una variable interna privada o haces un refactor del método que sigue devolviendo lo mismo, es un *Bad Test*.

- **Frontend (UI Components)**: Testear lo que ve e interactua el usuario. Por ejemplo (Testing Library), buscar y hacer click en texto de botón "Enviar", en vez de buscar `div > .classBtnSubmit`.
- **Backend**: Testear la lógica de Estado de Salida. Comprobar que tras usar AuthService regresa el Token, en lugar de testear si se mandó a llamar un hash privado subyacente de la librería `bcrypt`.

---

## 4. Técnicas de Aislamiento (Mocks y Spies)

Una Unidad = 1 Concepto/Función aislada.

- Si el código hace llamadas a una Base de Datos, Servicio S3 de Amazon o un Stripe/Paypal externo, SE DEBE simular (Mock).
- Las pruebas Unitarias se deben ejecutar en su totalidad (~300 tests) en menos de 5 segundos a nivel consola local, y sin conexión a Internet (Airplane mode-friendly).
- Diferencia de Conceptos:
  - **Mock / Stub**: Provee datos falsos de respuesta pre-fabricada ("Cuando el API pida X dato, devuélvele esta const 'Y' al instante").
  - **Spy**: Observar una función para afirmar si fue invocada. (Ej. "Espera a que se corra y asegúrate que mi función notificadora de SMS fue llamada exactamente 1 vez con este número falso").

---

## 5. Casos Límite a Cubrir Obligatoriamente

Un desarrollador siempre debe testear:

1. El Camino Feliz (Valores de inicio razonables).
2. Entradas Numéricas Negativas o en Cero donde no apliquen.
3. Strings Vacíos o Formatos estúpidamente largos (Null, Empty, Boundary).
4. El peor caso: Qué excepción arroja la clase si todo el servicio simulado backend (mock) revienta o colapsa de error HTTP. (¿Se atrapa el try-catch ordenadamente?).
