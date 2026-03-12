# Estándar de Gestión de Estado (State Management)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Aplicaciones Frontend basadas en componentes (React, React Native, Vue, etc).
**Ubicación sugerida**: `docs/frontend/` o `docs/architecture/`

---

## 1. Propósito

Definir cuándo usar estado local, cuándo usar contextos y qué herramienta global utilizar (Zustand, Redux, Context API) para evitar la prop-drilling, fugas de memoria, re-renders masivos y el "spaghetti state" en aplicaciones frontend escalables.

---

## 2. Tipos de Estado

Antes de instalar una librería, se debe clasificar la información a guardar:

### 2.1 Estado Local (UI State)
- **Alcance**: Un solo componente.
- **Ciclo de vida**: Nace y muere con el componente.
- **Herramienta**: `useState` o `useReducer` nativo de React.
- **Ejemplos**: `isModalOpen`, `inputValue`, `accordionExpanded`.

### 2.2 Estado Global de UI / App (App State)
- **Alcance**: Múltiples componentes, posiblemente distantes en el árbol.
- **Ciclo de vida**: Durante la sesión de la App, cruzando ruteo de pantallas.
- **Herramienta**: Context API, Zustand, Redux o Jotai.
- **Ejemplos**: `isSidebarOpen`, `theme` (Oscuro/Claro), `currentUser` autocompleto.

### 2.3 Estado de Servidor / Caché (Server State)
- **Alcance**: Datos remotos sincronizados con el backend.
- **Ciclo de vida**: Invaluable, expira a los X minutos, se re-gestiona al reconectar.
- **Herramienta**: React Query (TanStack Query), SWR, RTK Query o Apollo.
- **Ejemplos**: Listas de `products`, de `invoices`, paginación externa.

---

## 3. El Problema del "Server State en el Store Global"

**Regla de Oro**: ¡No meter el resultado de un `fetch()` en Zustand o Redux a menos que sea algo puramente estático!
El estado que pertenece al backend es asíncrono e impredecible (carga, red, errores). Herramientas como Redux no fueron creadas para lidiar eficientemente con cacheo y retryers; usar una herramienta dedicada a la sincronización servidor (como React Query) resolverá la mayor parte de los dolores de cabeza de "Global State".

---

## 4. Eligiendo la Herramienta para "App State"

Si de verdad se requiere almacenar estado Global App (como un carrito de compras interactivo local o variables temporales de flujos largos de creación en pasos):

### 4.1. Zustand (Opción Preferida Moderna)
Es ligero y no requiere envolver toda la aplicación en largos `<Providers>`.
- Permite crear stores granulares y combinables.
- Selector-friendly: Protege al componente de re-renderizarse si cambia algo del store que él no suscribió. Ej. `const bears = useStore((state) => state.bears)` (El componente solo reacciona a `bears`, no al resto de variables en Zustand).

### 4.2. React Context API
- **Cuándo usarlo**: Para inyectar dependencias casi estáticas (`ThemeProvider`, `AuthContext`) en una porción baja de la aplicación.
- **Peligro**: Si el objeto "value" cambia mucho o con gran rapidez, **TODOS** los consumidores del Context van a volver a renderizarse, aunque solo necesiten leer una llave. No sirve para valores altamente mutables (juegos, canvas, trackers).

### 4.3. Redux / Redux Toolkit (Legacy/Corp)
- Reservado únicamente cuando se trabaja sobre arquitecturas corporativas gigantescas, complejas, con la necesidad de reproducir estados, hacer "Time-Travel debugging" explícito mediante Redux DevTools, o usar Middlewares potentes (Sagas/Thunks avanzados).
- De usarse, emplear SÓLO "Redux Toolkit" (`@reduxjs/toolkit`). Redux vainilla base queda desaprobado por boilerplate pesado.

---

## 5. Diseño Centralizado de "Stores"

Si se opta por un estado Global (e.j Zustand):

- **No crear un "Mega Store"**: Evitar un archivo `useAppStore` que albergue el carrito de compras, la cámara temporal, y el modal lateral.
- **Dividir por Dominios**: Crear algo como `useCartStore`, `useDashboardUIStore`.
- **Inmutabilidad Absoluta**: Modificar el estado local global SIEMPRE regresando nuevas referencias (Zustand/Redux internamente pueden usar Immer, permitiendo la ilusión óptica de mutación como `state.items.push()`, aprovechar esa capa pero ser consciente de la Inmutabilidad real debajo).
