# Estándar de UX Copywriting

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier interfaz de usuario web, móvil, de consola o de agente conversacional.
**Ubicación sugerida**: `docs/design/copy/` o en un sistema de diseño definido.

---

## 1. Propósito

Definir el tono de voz, la escritura de los mensajes de error, las notificaciones y el microcopy de la aplicación. Un buen UX Copywriting debe sonar humano, evitar jergas de sistema, guiar al usuario e incrementar la confianza en el producto.

---

## 2. Los 3 Principios del Copy

El contenido de las interfaces debe ser:

1. **Claro**: El usuario debe entender qué pasa sin tener que releer. Elimina la jerga técnica. (En vez de "Error 404 de red", usar "No pudimos conectarnos").
2. **Conciso**: Ve al grano. La atención del usuario es corta; quita palabras de relleno. (En vez de "Haz clic aquí para confirmar y guardar tus datos", usar "Guardar datos").
3. **Útil**: Ayuda a avanzar en el flujo. Si hay un problema, ofrece una solución clara o el siguiente paso.

---

## 3. Anatomía del Microcopy

### 3.1. Call to Actions (CTAs) / Botones
- Empezar con un **verbo de acción fuerte**.
- Deben describir exactamente qué va a pasar.
- *Bien:* "Crear cuenta", "Enviar mensaje", "Eliminar archivo".
- *Mal:* "Aceptar", "Ok", "Haga clic aquí".

### 3.2. Empty States (Estados Vacíos)
El texto cuando no hay información debe ser motivador.
- **Título**: "Aún no tienes mensajes"
- **Subtítulo**: "Cuando te contacten nuevos clientes, aparecerán aquí."
- **Botón**: "Crear nuevo mensaje"

### 3.3. Textos de Apoyo (Tooltips, Inputs, Placeholders)
- **Label (etiqueta)**: Debe ser el nombre exacto de la información (ej. "Contraseña").
- **Placeholder**: Un ejemplo de formato (ej. "ejemplo@correo.com") - *No usar el placeholder para reemplazar la etiqueta superior*.
- **Helper text**: Aclara reglas antes del error (ej. "Mínimo 8 caracteres, al menos un número").

---

## 4. Notificaciones y Estados (Success/Error/Warning/Info)

Todo sistema tiene 4 tipos de retroalimentación; cada uno debe tener un tono adecuado.

| Tipo | Tono | Estructura | Ejemplo |
|------|------|------------|---------|
| **Éxito (Success)** | Positivo y breve. | Confirmar de qué se trata y cerrarlo. | "Archivo subido correctamente." |
| **Error** | Comprensivo, calmado, constructivo. | Qué pasó + Por qué + Cómo arreglarlo. | "Contraseña incorrecta. Inténtalo de nuevo o restáurala." *(Nunca culpar al usuario)* |
| **Advertencia (Warning)** | Precavido pero sin asustar. | Advertir consecuencia + Acción preventiva. | "Se perderán los cambios no guardados. ¿Deseas salir?" |
| **Informativo (Info)** | Neutral, transparente. | Situación general + Tiempo esperado. | "Estamos procesando tu pago, tomará unos segundos." |

---

## 5. Prevención de Errores vs. Manejo de Errores

Siempre es preferible **prevenir** con buen copy antes que mostrar un mensaje de error. Si ocurre un error, en un buen UX Copywriting:
- Jamás digas "el sistema falló" o muestres el log de excepción. 
- Jamás acuses al usuario (ej. "Ingresaste datos inválidos").
- Dirígete a la acción de recuperación.

---

## 6. Inclusión, Accesibilidad y Localización

- **Etiquetas Alt y ARIA Labels**: Todo botón con icono debe tener una descripción legible por Screen Readers.
- Evitar usar palabras basadas en interacción pura de hardware ("haz clic", "mira arriba"). Preferir "selecciona", "revisa las opciones".
- Asegurar que el texto sea neutral, no discriminatorio, y que pueda traducirse fácilmente a otros idiomas (sin depender de mexicanismos, modismos, o juegos de palabras locales cuando el producto es global o regional).
