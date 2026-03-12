# Estándar PHP Cero "Fat Controllers" (Laravel)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier monolito o API desarrollada con PHP moderno (Laravel, Symfony).
**Ubicación sugerida**: `docs/backend/`

---

## 1. Propósito

Evitar que los controladores crezcan sin control ("Fat Controllers"). Un controlador gigante con 1500 líneas de código es in-testeable, frágil e imposible de leer para mantenimiento.

---

## 2. El Controlador Delgado y sus 3 Promesas

La única responsabilidad de un Controlador es hacer puente entre el exterior (HTTP) y el interior (Reglas de negocio). **Un Controlador de Laravel NUNCA debe superar las 100 líneas**.

Se le permiten hacer estrictamente 3 cosas:
1. **Recibir y Validar** (vía `FormRequests` estrictos de Laravel, nunca validando en la propia función del controller).
2. **Pasar la batuta** hacia la Capa de Lógica de Negocio (Service / Action).
3. **Servir la Respuesta** (retornar un JSON Resource o un HTML View).

*Ejemplo Perfecto*:
```php
public function store(StoreOrderRequest $request, ProcessOrderService $service)
{
    // 1. En este punto $request ya fue Validado y Autorizado silenciosamente por Laravel.
    
    // 2. Pasamos la capa al Servicio
    $order = $service->execute($request->validated(), $request->user());

    // 3. Devolvemos respuesta transformada
    return new OrderResource($order);
}
```

---

## 3. Patrón de Servicios (Service o Actions)

Toda lógica real "Si pasa X, entonces suma Y, envía mail Z y descuenta la tabla W" vive en la capa de Servicios.

- Crearlos manualmente en `app/Services/` o `app/Actions/`.
- **Clases Invokables / Único Propósito**: Idealmente, un Servicio debería enfocarse en solo hacer una cosa principal (ej. `CreateInvoiceAction`).
- Se debe inyectar por dependencias al Controller.
- Estos Servicios son los únicos que envían mails, llaman a APIs externas, tiran colas o procesan pagos masivos.

---

## 4. Patrón Repository (Aislamiento de la Base de Datos)

El Eloquent ORM de Laravel puede volverse tóxico si cruzas `User::where('status', 'active')->orderBy('date')->get()` por toda la aplicación, atando tu código permanentemente a esa tabla exacta y a esos nombres de columna exactos.

- **Para consultas complejas**: Aislar el acceso a la base de datos a través de una carpeta `app/Repositories/`.
- El servicio interactúa con el Repositorio (ej. `$this->userRepository->findActiveUsers()`), perdiendo el conocimiento de si la tabla se llama 'users' o cómo está estructurada internamente bajo el ORM.

---

## 5. Try/Catch Transaccionales

Cuando la capa de Servicio debe escribir en varias tablas (ej. Crea un Usuario, le crea un Perfil, le asigna Rol y genera una tabla de Preferencias), ESTO OBLIGA AL USO DE TRANSACCIONES DE BD.

```php
use Illuminate\Support\Facades\DB;

public function execute(array $data) {
    return DB::transaction(function () use ($data) {
        $user = User::create([...]);
        Profile::create(['user_id' => $user->id, ...]);
        $user->assignRole('client');
        
        return $user;
    });
}
```
Si una falla, todo el bloque se des-escribe (Rollback automático) evitando basurar la base de datos con tablas a la mitad (orphan records).
