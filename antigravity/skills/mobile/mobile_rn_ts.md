# Estándar de React Native con TypeScript

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Aplicaciones móviles desarrolladas con React Native (CLI o Expo).
**Ubicación sugerida**: `docs/mobile/` o `docs/frontend/`

---

## 1. Propósito

Establecer las bases arquitectónicas y de código para aplicaciones React Native, asegurando el rendimiento en hilos de UI, la seguridad de tipos estrictos con TypeScript y el uso adecuado de Hooks funcionales.

---

## 2. Tipado Estricto (TypeScript)

TypeScript es obligatorio en todo proyecto nuevo.

- **Interfaces vs Types**: Usar `type` para uniones e intersecciones simples, y usar `interface` para objetos y props de componentes, ya que facilitan la extensión.
- **`any` Cero Absoluto**: Evitar por completo el uso de `any`. Si el tipo real es desconocido, usar `unknown` e ingeniar un Type Guard (`if (typeof x === "string")`).
- **Definición de Props**:
```tsx
interface ButtonProps {
  title: string;
  onPress: () => void;
  variant?: 'primary' | 'secondary'; // Union types para opciones restringidas
}
```
- **Navegación Tipeada**: Al usar React Navigation, definir siempre un archivo `types/navigation.ts` con el `RootStackParamList` estricto para evitar *params* inseguros entre pantallas.

---

## 3. Uso y Creación de Hooks

Toda lógica compleja que no sea UI pura debe abstraerse en Custom Hooks (`useFetchUser`, `useLocation`).

### 3.1. Reglas Oficiales
- Llamar hooks siempre en el nivel superior del componente. No anidarlos en bucles ni condicionales.

### 3.2. Rendimiento (Performance) con Hooks
En móvil, el hilo de JavaScript (JS Thread) compite agresivamente con el hilo gráfico (UI Thread). 
- **`useMemo`**: Usarlo **únicamente** cuando una función computacional sea altamente costosa (ej. filtrar listas de miles de items). No usarlo prematuramente por "optimización", de lo contrario cuesta más la memorización que el renderizado.
- **`useCallback`**: Emplearlo si se pasa una función por prop a un componente hijo **que está envuelto en `React.memo`**, para evitar que el hijo se re-renderice innecesariamente enviando una nueva referencia de función en cada ciclo del padre.
- **`useEffect`**: Mantener limpio el arreglo de dependencias. ¡Cuidado con funciones como dependencias sin estar memorizadas! Limpiar listeners (desmontaje).

---

## 4. Estilos y Layout (Flexbox)

- **StyleSheet**: Usar siempre `StyleSheet.create({})` de React Native (o utilerías comprobadas tipo NativeWind/Tailwind o Reanimated stylesheets). Evitar los estilos en línea (inline styles) pesados pues se re-crean en cada render.
- **Flexbox**: Toda la UI en RN se basa en layouts Flexbox.
  - Dimensiones fijas deben evitarse para pantallas completas, permitir flexibilizar siempre.
  - Soportar Safe Area: Usar `SafeAreaView` o hooks como `useSafeAreaInsets` de `react-native-safe-area-context` para que el "Notch" no tape información.

---

## 5. Prácticas Móviles Clave

### 5.1. Listas Largas
- **NUNCA usar `.map()` dentro de un `<ScrollView>`** para listas dinámicas grandes.
- Usar siempre `<FlatList>` o `<FlashList>` (de Shopify) definiendo correctamente `keyExtractor` (idealmente el ID como string) y `getItemLayout` (si todos los componentes miden igual) para evitar cuellos de botella de renderización.

### 5.2. Imágenes
- Usar componentes de imágenes cacheadas (`react-native-fast-image` o componentes visuales de Expo Image) para listados visuales, ya que el componente `<Image>` nativo no tiene un manejo sofisticado del caché de memoria a gran escala.

### 5.3. Teclado
- Toda pantalla de captura de formulario debe estar envuelta en un `KeyboardAvoidingView` o librería análoga (`react-native-keyboard-aware-scroll-view`) para evitar que el OS tape los *inputs* inferiores.
