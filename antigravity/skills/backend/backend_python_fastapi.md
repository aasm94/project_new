# Estándar de Backend Python con FastAPI

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Microservicios, APIs de procesamiento de datos o IA desarrollados en Python con FastAPI.
**Ubicación sugerida**: `docs/backend/` o `docs/ai/`

---

## 1. Propósito

Garantizar la construcción de servicios web robustos, de alto rendimiento y puramente tipados mediante FastAPI, capitalizando la inyección de dependencias, las validaciones automáticas con Pydantic y el asincronismo nativo.

---

## 2. Tipado y Validación de Datos (Pydantic)

FastAPI respira gracias a Pydantic. Python debe escribirse con todo el rigor de tipos estáticos modernos.

- Siempre usar **Type Hints** de manera exhaustiva en funciones, parámetros y respuestas (ej. `def process(data: List[int]) -> dict:`).
- **Modelos Pydantic (Schemas)**: Todo payload de entrada (Request) y todo payload de salida (Response) debe ser regido por un Pydantic BaseModel (ej. `class UserCreate(BaseModel)`). 
- Nunca recibir Diccionarios anónimos (`dict`) ni devolver objetos sin esquemas claros (`response_model=UserResponse`).

---

## 3. Asincronismo (Async / Await) vs Sincronismo (Def)

FastAPI permite ambos mundos, pero es vital no mezclarlos equivocadamente.

- **`async def`**: Usarlo CADA VEZ que el endpoint deba interactuar con librerías asíncronas de I/O (Bases de Datos con drivers asíncronos como `asyncpg` o `Motor`, o peticiones red como `aiohttp` / `httpx`).
- **`def` (Síncrono normal)**: Usarlo si se están corriendo procesos pesados de CPU (operaciones matemáticas densas, Pandas, Numpy) o si la librería de la DB es síncrona/bloqueante (ej. psycopg2 puro, SQLAlchemy clásico). FastAPI enviará mágicamente estas funciones a un threadpool externo para no bloquear el Asycn Event Loop.

### 3.1 Cuidado crítico
NUNCA utilices llamadas I/O bloqueantes (ej. `requests.get()` o `time.sleep()`) dentro de una función definida con `async def`, terminará bloqueando a todo el servidor.

---

## 4. Inyección de Dependencias (Dependencies)

El poderoso `Depends()` de FastAPI debe utilizarse para extraer lógica repetitiva y gestionar el ciclo de vida.
- **Casos de Uso Primordiales**:
  1. Conexiones y Sesiones a Base de Datos (ej. proveer un manejador de DB que se cierra tras inyectarse al path op).
  2. Verificación de Autenticación / JWT.
  3. Paginación y Query Parameters comunes.
- **Seguridad**: Proteger las rutas pasándole dependencias en los routers (ej. `dependencies=[Depends(get_current_active_user)]`).

---

## 5. Estructura de Proyecto (Layout)

Aunque FastAPI no impone estructura, se recomienda seguir convenciones robustas (similares a Django o NestJS segmentados) en vez de un solo archivo `main.py` de 2000 líneas.

```
app/
├── api/             # Routers de FastAPI separados (ej. /users, /items)
├── core/            # Configuración (Pydantic BaseSettings), Seguridad
├── crud/            # CRUD Reutilizable (Consultas puras a BD)
├── db/              # Modos, Sesiones (SQLAlchemy, Motor config)
├── models/          # Modelos de ORM (Bases de datos relacional/mongo)
├── schemas/         # Modelos de Pydantic (InOut Data Contracts)
└── main.py          # Ensamblaje y montado de Routers / Middleware
```

---

## 6. Variables y Configuración

Toda la configuración y variables de `.env` deben centralizarse usando la clase `BaseSettings` de Pydantic en `core/config.py`.
- Esto aporta validación estricta y auto-completado tipado sobre claves sensibles en todo el repositorio.
