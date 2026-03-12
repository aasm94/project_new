# Estándar de React Web + Tailwind CSS

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Aplicaciones web desarrolladas con React (Vite, Next.js, Remix) estilizadas con Tailwind CSS.
**Ubicación sugerida**: `docs/frontend/`

---

## 1. Propósito

Definir lineamientos técnicos para el desarrollo de interfaces modernas y ultra-rápidas mediante la creación de componentes modulares, responsivos y fácilmente mantenibles combinando React y la filosofía "Utility-First" de Tailwind CSS.

---

## 2. Componentización con Tailwind

La mayor queja contra Tailwind es que engorda el HTML con clases. La solución no es usar `@apply`, la solución es **extraer a componentes de React**.

### 2.1 Evitar @apply
No usar directivas `@apply` (crear clases CSS personalizadas uniendo utilidades Tailwind). Pierde el sentido modular del framework. Si hay un botón que repite estilos, **se debe crear un componente `<Button />`**.

*Ejemplo correcto de abstracción*:
```tsx
// components/Button.tsx
export const Button = ({ children, variant = "primary" }) => {
  const baseClasses = "px-4 py-2 rounded-md font-semibold transition-all";
  const variants = {
    primary: "bg-blue-600 text-white hover:bg-blue-700",
    secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300",
  };
  
  return (
    <button className={`${baseClasses} ${variants[variant]}`}>
      {children}
    </button>
  );
};
```

*(Para concatenación avanzada o choques de clases, usar librerías como `clsx` y `tailwind-merge` o la utilidad fusionada `cn()`).*

---

## 3. Disposición y Grid System

### 3.1. Flexbox vs Grid
- **Flexbox (`flex`)**: Usar para alineaciones lineales y dinámicas rápidas (ej. barra de navegación, menús de fila sencilla, centrado vertical y horizontal).
- **CSS Grid (`grid`)**: Usar para la arquitectura gruesa de la pantalla y listas matriciales. Es mucho más mantenible `grid-cols-1 md:grid-cols-3` que intentar arreglar *margins* al vuelo utilizando Flex.

### 3.2. Espaciado Consistente
Seguir estrictamente la escala de espaciado nativa de Tailwind (`p-4`, `m-6`, `gap-8`). 
**Prohibido** usar valores arbitrarios incontrolados (ej. `w-[234px]`) a menos que el diseño exija precisión de pixel para un asset incrustado o posición absoluta forzada.

---

## 4. Mobile-First & Responsividad

Todo el desarrollo web moderno debe empezar por la vista más pequeña.
- El conjunto inicial de clases dictará el estilo móvil.
- Utilizar los prefijos de breakpoint (`sm:`, `md:`, `lg:`, `xl:`) solo cuando cambia el diseño hacia pantallas más grandes.

*Ejemplo de flujo de pensamiento:*
```html
<!-- Empieza colapsado (columna central). Pasa a horizontal en tables (md). -->
<div className="flex flex-col md:flex-row gap-4">
  ...
</div>
```

---

## 5. Modo Oscuro (Dark Mode) y Tematización

Soportar `dark mode` desde la configuración inicial.
- Toda especificación de color debe ir acompañada de su variante osura (e.j. `bg-white dark:bg-gray-900 border-gray-200 dark:border-gray-800 text-gray-900 dark:text-gray-100`).
- Apoyarse en las variables CSS globales para definir colores de marca (ej. definir los RGB en global.css y mapear el `tailwind.config` usando rgba/vars), lo que permite "temas" que sobreescriban a los grises nativos.

---

## 6. Integración y Comportamientos (Hooks/Accessibility)

1. **Focus Rings (A11y)**: No quitar estilos de contorno ("outline none") sin poner uno sustituto. Tailwind facilita el `focus:ring` y `focus:outline-none`.
2. **Animaciones Sutiles**: Aprovechar utilidades como `transition-all duration-200 ease-in-out` para suavizar cualquier *hover* en elementos interactables sin sobrecargar la UI visual.
3. **Headless UI**: En vez de luchar para reconstruir la lógica de un acordeón o Menú Desplegable con accesibilidad para teclado nativa, se sugiere acoplar combinaciones de componentes Headless (Radix UI, HeadlessUI de Tailwind, o Shadcn UI).
