# Estándar de Manejo de Errores (Error Handling)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier aplicación (Frontend, Backend, Móvil) que procese asincronismo o consuma datos propensos a fallar.
**Ubicación sugerida**: `docs/coding/` o `docs/architecture/`

---

## 1. Propósito

Asegurar que la aplicación NUNCA se "congele", colapse silenciosamente (Unhandled Exceptions) o muestre errores incomprensibles de sistema al usuario final. El manejo de errores debe ser proactivo, registrarse para el equipo de desarrollo, y devolver una respuesta amigable.

---

## 2. Regla Universal: Try / Catch

Toda operación que salga de la memoria RAM del lenguaje (Llamadas a Base de Datos, Peticiones de Red HTTP, Lectura/Escritura de Archivos, Parseo de JSON ajeno) **DEBE estar forzosamente envuelta en un control de excepciones o Try/Catch**.

### 2.1 Backend / API
Si ocurre un error, la app backend debe:
1. Atrapar la excepción real en un bloque catch.
2. Hacer **Log (Registro)** detallado del error subyacente hacia la consola (Winston/Pino, AWS Cloudwatch, Datadog) alertando a los desarrolladores `logger.error("Error BD: " + err)`.
3. Retornar una respuesta formateada **sin romper el servicio** Node/Python/PHP. 
   - NUNCA enviando el "Stack Trace" real de base de datos o sistema en el JSON hacia el usuario final (grave vulnerabilidad de seguridad).
   - Devolver un mensaje humano: `{ "status": "error", "message": "No pudimos procesar la operacion. Intente más tarde." }` con un Status Code `5xx` o `4xx`.

### 2.2 Frontend / UI
El cliente debe absorber los errores devolviendo retroalimentación visual amigable.
- Jamás dejar la pantalla en blanco (White Screen of Death).
- Apagar todos los Spinners / Loadings en el bloque `finally {}`.
- Mostrar un "Toast" de notificación, Modal o mensaje de Validación ("Credenciales inválidas").

---

## 3. Fallo Temprano (Fail Fast / Guard Clauses)

En lugar de construir enormes murallas condicionales (`if...else if...else if`) anidadas que engordan el código (el llamado Anti-Patrón Arrow/Flecha), se debe usar el patrón de **Cláusulas de Guarda** (Guard Clauses).

Revisar primero los casos de error, arrojar una excepción, y dejar el "Camino Feliz" (Happy Path) al final de forma natural.

- *Mal*:
```javascript
function processData(user, data) {
  if (user) {
    if (user.isActive) {
      if (data) {
        // ... Logica de Venta Gigante de 60 líneas
      } else {
        throw new Error("Sin datos");
      }
    } else {
      throw new Error("Inactivo");
    }
  } else {
    throw new Error("No hay usuario");
  }
}
```

- *Bien (Fail Fast / Return Early)*:
```javascript
function processData(user, data) {
  if (!user) throw new Error("Acceso denegado: Usuario no encontrado");
  if (!user.isActive) throw new Error("Acceso denegado: Usuario inactivo");
  if (!data) throw new Error("Error en petición: Payload vacío");

  // ... Logica de Venta de 60 líneas libre de indentaciones.
}
```

---

## 4. Tipos Custom de Errores (Custom Error Classes)

Dependiendo de la severidad, en aplicaciones construidas orientadas a objetos/tipos (Ej. TS, Python) se deberían crear clases específicas para poder ser atrapadas de diferente manera por el Middleware Global.

```typescript
// Ejemplos comunes
class ValidationError extends Error {}  // Se traduce a HTTP 400
class UnauthorizedError extends Error {} // Se traduce a HTTP 401
class DatabaseConnectionError extends Error {} // HTTP 500 + Logueo Crítico + Alerta PagerDuty
```
