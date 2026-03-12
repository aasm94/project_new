# Estándar de Diseño de API REST

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Interfaces de Programación Web usando HTTP, microservicios orientados a recursos o BFFs (Backend for Frontend).
**Ubicación sugerida**: `docs/api/` o documentado directo en herramientas Open API.

---

## 1. Propósito

Garantizar la construcción de APIs uniformes, predecibles e idotempotentes que sean fáciles de consumir, documentar, probar y escalar mediante el uso estricto y uniforme de los principios de diseño Restful y los estándares de la web.

---

## 2. Nomenclatura y Diseño de URIs

Los URLs deben representar siempre **Recursos**, no acciones.
- **Formato**: Sustantivos, siempre en **plural**, con minúsculas y `kebab-case`.
- **Anidación estricta**: Agrupar recursos anidados lógicamente pero no exceder ~2-3 niveles de profundidad para mantener URLs limpias.
  
*Correcto*:
- `GET /users` (Lista todos los usuarios)
- `GET /users/123/invoices` (Listar facturas del usuario 123)

*Incorrecto*:
- `POST /createUser` (Es una acción y contiene verbos).
- `GET /invoicesByUser?userId=123` (Debe ser parte del path en REST).

---

## 3. Uso Predictivo de Verbos HTTP

Los verbos dictan la intención de lo que se le hace al recurso.

| Verbo | Propósito | Operación (CRUD) | ¿Idempotente? |
|-------|-----------|------------------|---------------|
| `GET` | Recuperar datos de uno o una lista de recursos. | Leer (Read) | Sí |
| `POST` | Crear un nuevo recurso. El ID lo asignará típicamente el backend. | Crear (Create) | **No** |
| `PUT` | Actualización o reemplazo total de un recurso existente. Si no existe y el cliente tiene el control total del ID deseado, puede crearse. | Reemplazar | Sí |
| `PATCH`| Modificación parcial de un recurso (solo envía las propiedades a cambiar). | Actualizar | Sí |
| `DELETE`| Borrar el recurso, ya sea lógico (soft) o físico. | Eliminar (Delete)| Sí |

---

## 4. Contratos JSON (Requests & Responses)

- El formato preferido para todo flujo de datos moderno es **JSON**.
- Evitar estructuras profundamente anidadas si no tiene sentido de negocio.
- **Nomenclatura**: Usar convencionalmente `camelCase` o `snake_case` consistentemente en todo el proyecto. Se sugiere `camelCase` en ecosistemas JavaScript/TypeScript y `snake_case` nativamente en Python, pero siempre mantener uno uniforme en la capa de salida HTTP (el API Gateway o Framework deben hacer la conversión auto si hace falta).

### 4.1. Respuestas Estándar
Se recomienda envolver listas con meta-data común:
```json
// GET /users (Lista e incluye Paginación)
{
  "data": [
    { "id": "1", "name": "Alice" },
    { "id": "2", "name": "Bob" }
  ],
  "meta": {
    "total": 50,
    "page": 1,
    "perPage": 20
  }
}
```

---

## 5. Códigos de Estado (HTTP Status Codes)

Usar y confiar en los códigos estándar HTTP y no colocar un status 200 y luego un body JSON enviando falsos errores de dominio.

- **2xx (Éxito)**
  - `200 OK`: La solicitud ha tenido éxito genérico.
  - `201 Created`: Un nuevo recurso ha sido existosamente fabricado (`POST`).
  - `204 No Content`: Proceso finalizado y no hay body para retornar (Típico en el éxito de un `DELETE`).

- **4xx (Errores del Cliente)**
  - `400 Bad Request`: Parámetros o Payload fallaron la capa de validación.
  - `401 Unauthorized`: Autenticación nula, token sin proveer o expirado.
  - `403 Forbidden`: Identidad correcta pero permisos de rol insuficientes o denegados.
  - `404 Not Found`: No existe la ruta HTTP, o se buscó un Recurso ID o query específico que no existe.
  - `422 Unprocessable Entity`: Error semántico en el body o violaciones explícitas de lógica del negocio, a pesar de usar correcto Formato de Tipado Estricto.

- **5xx (Errores del Servidor - Corregir y Monitorear)**
  - `500 Internal Server Error`: Falla genérica no tratada ni en try/catch. Un `500` debe ser la excepción, no la norma.
  - `502 Bad Gateway` / `504 Gateway Timeout`.

---

## 6. Estándares de Seguridad

1. **HTTPS Absoluto**: Nunca transmitir data por API REST genérica usando texto claro en `HTTP`. TLS 1.2+ obligatorio.
2. **Tokens / Auth**: 
   - Transmitir la identificación del cliente mediante cabecera Authorization Bearer Token (`Authorization: Bearer eyJhb...`). 
   - No poner secretos, tokens o passwords directos explícitamente en la URL de Request.
3. **CORS**: Definir y filtrar correctamente cabeceras de `Access-Control-Allow-Origin` según quién consumirá el endpoint.
4. **Rate Limiting / Throttling**: Proteccer los microservicios usando contramidas que limiten Peticiones Por Instante.

---

## 7. Versionado
Si existen integraciones externas y la API es pública a terceros o apps móviles con versiones rígidas de releases:
- Usar el routeo por prefijos (ej. `/api/v1/users` y migrar luego a `/api/v2/users`).
- Documentar en sistemas Swagger/OpenAPI y avisar fechas de *Deprecation*.
