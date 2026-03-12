# Estándar de Backend PHP con Laravel

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Monolitos, APIs y sistemas backend desarrollados en PHP usando el framework Laravel.
**Ubicación sugerida**: `docs/backend/`

---

## 1. Propósito

Establecer un patrón de arquitectura claro dentro del ecosistema de Laravel que evite los "Fat Controllers" (controladores inflados) y "Fat Models" (modelos sobrecargados), promoviendo la separación de responsabilidades, testabilidad y mantenibilidad.

---

## 2. Arquitectura de Capas (Action / Service Pattern)

El patrón MVC tradicional de Laravel tiende a fallar en aplicaciones complejas. Se debe utilizar un enfoque basado en Servicios (o Actions).

### 2.1. Controladores (Controllers) delgados
El único propósito de un controlador es recibir el Request, validarlo (mediante FormRequests), pasarlo a la capa de lógica (Service/Action), y retornar la respuesta (JSON o View).
- **Regla Basa**: Un controlador **NUNCA** debe contener reglas de negocio complejas ni interactuar directamente con la base de datos más allá de simples `find()`.
- **Código de Error Limpio**: Devolver responses uniformes usando `Resource` classes para transformar los modelos.

### 2.2. Capa de Servicios o Actions (Lógica de Negocio)
- Crear carpetas `app/Services/` o `app/Actions/`.
- Aquí residen los cálculos, algoritmos, envío de emails, transacciones de múltiples tablas, llamadas a APIs externas.
- Promueve la reutilización: Un servicio (ej. `CreateInvoiceAction`) puede ser llamado por un Controller, un Artisan Command o un Job de colas.

---

## 3. Validaciones y Requests

Jamás usar `$request->validate()` en el controlador si la lógica consta de más de dos campos sencillos.
- **FormRequests**: Todo endpoint de creación o actualización debe inyectar un `CustomRequest` (ej. `php artisan make:request StoreUserRequest`).
- **Autorización**: Las validaciones de "quién" puede hacer la acción van en el método `authorize()` del FormRequest o en **Policies** estrictas.

---

## 4. Eloquent ORM: Modelos e Interacciones BD

- **Evitar N+1 Queries**: Usar siempre eager loading (`with()`, `load()`) cuando se iteran relaciones. Activar el estricto `Model::preventLazyLoading(!app()->isProduction())` durante el desarrollo local en el `AppServiceProvider`.
- **Scopes**: Encapsular lógicas de consultas frecuentes usando Local Scopes (ej. `public function scopeActive($query)`) para mantener los queries limpios en el Service.
- **Transacciones**: Cualquier Service que modifique más de una tabla a la vez DEBE envolver la operación en `DB::transaction(function() { ... })`.

---

## 5. Colas y Tareas Asíncronas (Jobs)

Toda operación que tome más de 200 milisegundos y no sea vital para el retorno inmediato al usuario debe ser empujada a una cola (Queue).
- **Ejemplos**: Envío de Mails/Notificaciones, Generación de PDFs pesados, Carga a S3, Consultas a APIs de terceros lentas.
- **Implementación**: Usar `php artisan make:job` y despachar la tarea (ej. `ProcessReport::dispatch($user)`).

---

## 6. Variables de Entorno y Configuración

- NUNCA usar la función global `env('Clave')` directamente dentro de los Controladores o Servicios.
- Siempre cargar el `env()` dentro de los archivos de configuración (`config/app.php`, `config/services.php`) y en el código usar el helper confiable `config('services.stripe.key')`.
- Esto previene apagones en la app si se ejecuta un `php artisan config:cache` en el servidor y limpia la lectura del archivo `.env`.
