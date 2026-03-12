# Estándar Core de TypeScript (React / React Native)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Todo el desarrollo frontend y móvil basado en TypeScript (React, React Native, Node.js).
**Ubicación sugerida**: `docs/coding/` o `docs/frontend/`

---

## 1. Propósito

Manejar correctamente los tipos en aplicaciones (especialmente envolviendo componentes de React y React Native) para tener seguridad en tiempo de compilación. **El objetivo principal es la erradicación absoluta del tipo `any`**.

---

## 2. La Regla de Oro: Declarar Cero `any`

El tipo `any` apaga el compilador de TypeScript y anula el beneficio de usar el lenguaje.
- Si no sabes exactamente la forma de la respuesta de un API ajena, usa `unknown`. Esto forzará al compilador a pedirte que "compruebes" o afirmes el tipo antes de intentar acceder a `data.nombre`.
- Activa `"noImplicitAny": true` y `"strict": true` en el `tsconfig.json` de todos tus proyectos desde el día 1.

---

## 3. Interfaces vs. Types

Usa ambas herramientas bajo la siguiente división de responsabilidades:

### 3.1 Interfaces para Objetos y Componentes
Siempre prefiere `interface` al declarar la forma de un Objeto puro y especialmente para las **Props** de un componente React. Esto las hace auto-documentadas y fácilmente extensibles.

```tsx
interface UserCardProps {
  id: string;
  fullName: string;
  isActive?: boolean; // El signo ? marca a la propiedad como opcional
  onPress: (id: string) => void;
}

export const UserCard = ({ id, fullName, isActive = false, onPress }: UserCardProps) => {
  return <TouchableOpacity onPress={() => onPress(id)}>...</TouchableOpacity>;
}
```

### 3.2 Types para Alias y Uniones
Usa `type` para combinaciones de tipos básicos, Uniones estrictas de strings o Intersecciones.

```typescript
type Direction = 'Up' | 'Down' | 'Left' | 'Right';
type EntityId = string | number;
```

---

## 4. Tipado de Hooks y Eventos en React

- **`useState`**: Tipar explicitly el estado si empieza nulo o como arreglo vacío.
  ```tsx
  // Bien
  const [users, setUsers] = useState<User[]>([]);
  const [modalType, setModalType] = useState<'create' | 'edit' | null>(null);
  
  // Mal (TS asume que es un arreglo de `never`)
  const [items, setItems] = useState([]);
  ```
- **Eventos del DOM (React Web)**:
  Para inputs y formularios web, se debe tipear el evento completo:
  ```tsx
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => { ... }
  ```

---

## 5. Exports y Modelos Centralizados

Toda interface o tipo de dato de negocio (ej. `User`, `Invoice`, `Product`) no debe vivir aislada en el mismo archivo del componente de React de la pantalla.
- Crear una carpeta `src/types/` o `src/models/` y exportar desde ahí las Interfaces puras.
- Importarlas en los componentes usando prefijos de tipo para no contaminar bundles:
  ```typescript
  import type { UserProfile } from '../types/user';
  ```
