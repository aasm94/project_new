# Estándar de Backend Node.js con Express

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: REST APIs, webhooks y microservicios operando sobre el V8 de Node JS usando framework minimalista (Express).
**Ubicación sugerida**: `docs/backend/`

---

## 1. Propósito

Regularizar la caótica flexibilidad nativa de Express.js y Node.js, imponiendo capas de estructura organizativa, uso estricto de validaciones, y un manejo robusto de excepciones asíncronas para construir APIs predecibles y sin memory leaks.

---

## 2. Arquitectura de tres capas

Dado que Express no opina sobre carpetas, se delega una convención estricta: Separación de Lógica (Router -> Controller -> Service).

1. **Router (`/routes`)**: Solo se encarga de declarar la URL, asociar middlewares (Validadores de Request / Auth) y delegar al Controller.
2. **Controller (`/controllers`)**: Recibe los parámetros sanitizados del request (req.body, req.params), le manda trabajo a la capa de Servicio, y devuelve (res.json) con los Códigos HTTP adecuados.
3. **Service (`/services`)**: La clase u objeto funcional verdadero. Alberga toda la Lógica de Negocio y reglas. No tiene conocimiento de qué es un "Request (req)" o un "Response (res)". Aquí hay Reusabilidad.

---

## 3. Manejo de Errores y Excepciones Asíncronas (async/await)

Node.js muere silenciosamente (Unhandled Promise Rejection) si el asincronismo se programa mal.

- **Jamás dejar promesas al aire**: Toda función asíncrona dentro de Express debe ser manejada.
- **Middleware Global de Error**: Emplear un `errorHandler(err, req, res, next)` genérico al final de todas las rutas de Express.
- **Envoltorio Async (Async Wrapper/Handler)**: En lugar de saturar de bloques `try/catch` visualmente cada controlador, se sugiere usar un wrapper utilitario (o una librería como `express-async-errors`) que encierre las llamadas del Controller y mande las fallas directamente al middleware global de forma automática.

```javascript
// Mal: Anidar siempre try/catches por no usar wrappers globales
exports.getUser = async (req, res, next) => {
  try {
    const user = await UserService.findById(req.params.id);
    res.json(user);
  } catch (error) {
    next(error); 
  }
};
```

---

## 4. Validación de la Entrada (Input Validation)

Toda API debe desconfiar del cliente.
- Validar SIEMPRE parámetros antes de que lleguen al Controller y mucho antes de tocar el Servicio o BB.DD.
- Usar middlewares validados como `Joi`, `Zod`, o `express-validator`.
- Separar la sanitización de esquemas en archivos (`/validators/user.validator.js`).

---

## 5. Prevención del "Event Loop Blocking"

El hilo de JS en Node es asíncrono pero Single-Thread (Único hilo CPU).
- Las llamadas de red y DB son delegadas asíncronamente (OK).
- Operaciones matemáticas inmensas, iteraciones muy pesadas o criptografía bloquean el Event Loop y colapsan a todos los clientes a la vez.
- **Solución**: Si se va a hacer parseo gigantesco o manipulación cruda de imágenes, despacharlo a **Worker Threads** nativos de Node, o delegar el servicio a microservicios externos (Python, Go).

---

## 6. Variables de Entorno y Seguridad

- Todo secreto debe vivir en `.env` apoyado por librerías como `dotenv`.
- Instalar y habilitar SIEMPRE `helmet` como primer middleware de seguridad general en cualquier app Express que salga a internet de forma perimetral.
- Bloquear en nivel API Gateway o por medio librerías (ej. `express-rate-limit`) APIs de autenticación de ataques de Fuerza Bruta básica.
- Logging (Pinos/Morgan/Winston): Nunca usar `console.log()` limpio en producción; utilizar loggers debidamente controlados (ej. `pino`) capaces de emitir salidas JSON fácilmente parseables por Data Dog o AWS Cloudwatch.
