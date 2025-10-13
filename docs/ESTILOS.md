# Guía de Estilos - MIDDAS E-commerce Template

Esta guía explica cómo trabajar con los estilos en el proyecto, incluyendo el sistema de colores dinámicos, tipografía y mejores prácticas para mantener una interfaz coherente y legible.

---

## Tabla de Contenidos

1. [Sistema de Colores](#sistema-de-colores)
2. [Tipografía y Colores de Texto](#tipografía-y-colores-de-texto)
3. [Contraste y Legibilidad](#contraste-y-legibilidad)
4. [Clases de Tailwind CSS](#clases-de-tailwind-css)
5. [Componentes y Estilos](#componentes-y-estilos)
6. [Mejores Prácticas](#mejores-prácticas)

---

## Sistema de Colores

### Configuración de Colores

Los colores se definen en `public/config/store-config.json` dentro del objeto `branding.colorScheme`:

```json
"colorScheme": {
  "name": "fresh",
  "primary": "#0066CC",      // Color principal (botones, links, elementos interactivos)
  "secondary": "#FFFFFF",    // Color secundario (fondos, tarjetas)
  "accent": "#00B4D8",       // Color de acento (destacados, badges)
  "background": "#F0F7FF"    // Color de fondo general del sitio
}
```

### Formato de Colores

- **Entrada**: Colores en formato **HEX** (`#RRGGBB`)
- **Procesamiento**: Conversión automática a **OKLCH** (espacio de color moderno)
- **Aplicación**: Variables CSS que usa Tailwind

### Variables CSS Generadas

El sistema genera automáticamente estas variables CSS:

```css
:root {
  --background         /* Color de fondo del sitio */
  --foreground         /* Color de texto principal */
  --primary            /* Color principal de la marca */
  --primary-foreground /* Texto sobre color principal */
  --secondary          /* Color secundario */
  --secondary-foreground /* Texto sobre color secundario */
  --accent             /* Color de acento */
  --accent-foreground  /* Texto sobre color de acento */
  --muted-foreground   /* Texto secundario/deshabilitado */
}
```

### Cómo Funciona la Conversión

El archivo `lib/color-utils.ts` se encarga de:

1. **Convertir HEX a RGB**: Parsea el color hexadecimal
2. **RGB a XYZ**: Transforma al espacio de color CIE XYZ
3. **XYZ a OKLCH**: Convierte al espacio OKLCH (Lightness, Chroma, Hue)
4. **Calcular contraste**: Determina automáticamente si el texto debe ser claro u oscuro

#### Ejemplo de Conversión

```
Input:  #0066CC (azul)
Output: oklch(0.543 0.156 251.234)
        ↓       ↓     ↓
        L       C     H
   (Luminosidad)(Saturación)(Tono)
```

---

## Tipografía y Colores de Texto

### Configuración de Tipografía

La tipografía se configura en dos niveles:

#### 1. Esquema de Fuentes

Define qué familias tipográficas usar:

```json
"typography": {
  "fontScheme": "modern"  // elegant | modern | classic
}
```

**Esquemas disponibles:**

| Esquema | Fuente Títulos | Fuente Cuerpo |
|---------|---------------|---------------|
| `elegant` | Playfair Display | Inter |
| `modern` | Inter (Bold) | Inter |
| `classic` | Lora | Merriweather |

#### 2. Colores de Texto (Opcional)

Define colores específicos para diferentes tipos de texto:

```json
"typography": {
  "fontScheme": "modern",
  "colors": {
    "heading": "#1a1a1a",  // Títulos (h1-h6)
    "body": "#2d2d2d",     // Texto normal (p, span, div)
    "muted": "#6b7280"     // Texto secundario (labels, captions)
  }
}
```

### Valores por Defecto

Si **NO** se especifican colores de texto, el sistema usa estos valores optimizados para legibilidad:

```javascript
const defaults = {
  heading: "oklch(0.17 0.01 60)",  // Casi negro (muy oscuro)
  body: "oklch(0.25 0.01 60)",     // Gris muy oscuro
  muted: "oklch(0.50 0.01 60)"     // Gris medio
}
```

### Aplicación de Colores de Texto

El sistema aplica automáticamente estos colores mediante CSS:

```css
/* Títulos siempre usan el color de heading */
h1, h2, h3, h4, h5, h6 {
  color: var(--heading-color) !important;
}

/* Texto general usa el color de body */
body, p, span, div {
  color: var(--body-text-color);
}

/* Texto secundario */
.text-muted-foreground {
  color: var(--muted-text-color) !important;
}
```

---

## Contraste y Legibilidad

### Reglas de Contraste Automático

El sistema implementa reglas inteligentes para garantizar legibilidad:

#### 1. Texto en Botones

Los botones **siempre** tienen texto con contraste óptimo:

```javascript
// Si el fondo es oscuro → texto blanco
// Si el fondo es claro → texto oscuro

function getContrastingForeground(backgroundHex) {
  const isDark = calculateLuminance(backgroundHex) < 0.5
  return isDark ? "oklch(0.98 0 0)" : "oklch(0.17 0.01 60)"
}
```

**Ejemplo práctico:**

```jsx
// Botón con fondo azul oscuro (#0066CC)
<Button className="bg-primary text-primary-foreground">
  Comprar  {/* ← Texto BLANCO automáticamente */}
</Button>

// Botón con fondo claro (#F0F7FF)
<Button className="bg-secondary text-secondary-foreground">
  Ver más  {/* ← Texto OSCURO automáticamente */}
</Button>
```

#### 2. Texto sobre Fondos

- **Fondo claro** (`background`): Texto oscuro por defecto
- **Fondo oscuro**: Texto claro por defecto
- **Tarjetas** (`card`): Texto adaptado al fondo

#### 3. Cálculo de Luminosidad

```javascript
// Fórmula W3C para luminosidad relativa
const luminance = 0.2126 * R + 0.7152 * G + 0.0722 * B

// Umbral de oscuridad
const isDark = luminance < 0.5
```

### Niveles de Contraste

El proyecto sigue las **WCAG 2.1 Guidelines**:

- **AA Normal**: Ratio mínimo 4.5:1 (texto normal)
- **AA Large**: Ratio mínimo 3:1 (texto grande)
- **AAA**: Ratio mínimo 7:1 (óptimo)

---

## Clases de Tailwind CSS

### Colores de Fondo

```jsx
className="bg-background"    // Fondo del sitio
className="bg-primary"       // Fondo color principal
className="bg-secondary"     // Fondo color secundario
className="bg-accent"        // Fondo color de acento
className="bg-card"          // Fondo de tarjetas
```

### Colores de Texto

```jsx
className="text-foreground"        // Texto principal
className="text-primary"           // Texto color principal
className="text-muted-foreground"  // Texto secundario
className="text-primary-foreground" // Texto sobre primary
```

### Bordes

```jsx
className="border border-border"   // Borde sutil
className="border-primary"         // Borde color principal
```

### Ejemplos Combinados

```jsx
// Botón primario
<Button className="bg-primary text-primary-foreground hover:bg-primary/90">
  Añadir al Carrito
</Button>

// Tarjeta con borde
<Card className="bg-card text-card-foreground border-border">
  <h3 className="text-foreground">Título</h3>
  <p className="text-muted-foreground">Descripción</p>
</Card>

// Badge de acento
<Badge className="bg-accent text-accent-foreground">
  Nuevo
</Badge>
```

---

## Componentes y Estilos

### Componentes UI Base

Todos los componentes están en `components/ui/` y siguen el sistema de tokens:

#### Button

```jsx
import { Button } from "@/components/ui/button"

// Variantes disponibles
<Button variant="default">Default</Button>    // bg-primary
<Button variant="secondary">Secondary</Button> // bg-secondary
<Button variant="outline">Outline</Button>     // border-primary
<Button variant="ghost">Ghost</Button>         // sin fondo
<Button variant="destructive">Delete</Button>  // rojo
```

#### Card

```jsx
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card"

<Card>
  <CardHeader>
    <CardTitle>Título</CardTitle>
  </CardHeader>
  <CardContent>
    Contenido
  </CardContent>
</Card>
```

#### Badge

```jsx
import { Badge } from "@/components/ui/badge"

<Badge variant="default">Badge</Badge>
<Badge variant="secondary">Badge</Badge>
<Badge variant="outline">Badge</Badge>
```

### Componentes Personalizados

#### ProductCard

```jsx
// components/product-card.tsx
<ProductCard product={product} />
```

Estilo:
- Fondo: `bg-card`
- Texto: `text-card-foreground`
- Precio: `text-primary` (destacado)
- Stock: `text-muted-foreground`

#### Header

```jsx
// components/header.tsx
<Header />
```

Estilo:
- Fondo: `bg-background/95` (semi-transparente)
- Links: `hover:text-primary`
- Badge carrito: `bg-primary text-primary-foreground`

---

## Mejores Prácticas

### 1. Colores de Texto

✅ **Hacer:**
```jsx
// Usar colores semánticos
<h2 className="text-foreground">Título</h2>
<p className="text-muted-foreground">Subtítulo</p>
```

❌ **Evitar:**
```jsx
// NO usar colores hardcoded
<h2 className="text-gray-900">Título</h2>
<p className="text-gray-500">Subtítulo</p>
```

### 2. Botones

✅ **Hacer:**
```jsx
// El texto contrasta automáticamente
<Button className="bg-primary text-primary-foreground">
  Click aquí
</Button>
```

❌ **Evitar:**
```jsx
// NO forzar colores de texto manualmente
<Button className="bg-primary text-white">
  Click aquí
</Button>
```

### 3. Fondos con Transparencia

✅ **Hacer:**
```jsx
// Usar opacidad con /
<div className="bg-primary/10 text-primary">
  Fondo sutil
</div>
```

### 4. Hover States

✅ **Hacer:**
```jsx
// Usar /90 para hover
<Button className="bg-primary hover:bg-primary/90">
  Hover suave
</Button>
```

### 5. Mínimo de Colores de Texto

**Limitarse a 2-3 tipos de colores de texto:**

1. **Heading** - Títulos principales (muy oscuro)
2. **Body** - Texto normal (oscuro)
3. **Muted** - Texto secundario (gris medio)

**Razón**: Demasiados colores crean inconsistencia visual y dificultan la lectura.

### 6. Excepciones de Color

La única excepción para texto claro es:

- Botones con fondo oscuro
- Badges con fondo oscuro
- Headers con fondo oscuro

En todos los casos, el sistema aplica **automáticamente** `text-primary-foreground` (blanco).

---

## Modificar Colores en Producción

### Paso 1: Editar store-config.json

```json
{
  "branding": {
    "colorScheme": {
      "name": "nuevo-tema",
      "primary": "#FF5733",      // Nuevo color principal
      "secondary": "#FFFFFF",
      "accent": "#FFC300",
      "background": "#FFF5E1"
    },
    "typography": {
      "fontScheme": "elegant",
      "colors": {
        "heading": "#2c2c2c",    // Opcional: personalizar
        "body": "#3a3a3a",
        "muted": "#757575"
      }
    }
  }
}
```

### Paso 2: Recargar la Aplicación

Los colores se aplican automáticamente al cargar la página. No requiere rebuild.

### Paso 3: Verificar Contraste

Usa herramientas como:
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [Coolors Contrast Checker](https://coolors.co/contrast-checker)

---

## Debugging de Colores

### Ver Variables CSS Aplicadas

Abre DevTools > Elements > Computed > Filter "color":

```css
:root {
  --background: oklch(0.97 0.005 60);
  --primary: oklch(0.543 0.156 251.234);
  --foreground: oklch(0.25 0.01 60);
  ...
}
```

### Verificar DynamicColorProvider

El componente inyecta un `<style>` con id `dynamic-theme-colors`:

```html
<head>
  <style id="dynamic-theme-colors">
    :root {
      --background: oklch(...) !important;
      ...
    }
  </style>
</head>
```

Si este `<style>` no aparece, revisar:
1. `DynamicColorProvider` está en `layout.tsx`
2. `store-config.json` se lee correctamente
3. No hay errores en consola

---

## Solución de Problemas

### Los colores no se aplican

**Causas comunes:**

1. **Colores hardcoded en componentes**
   - Solución: Reemplazar con clases semánticas (`text-foreground`, `bg-primary`)

2. **CSS con mayor especificidad**
   - Solución: Usar `!important` o aumentar especificidad

3. **store-config.json mal formateado**
   - Solución: Validar JSON en [jsonlint.com](https://jsonlint.com/)

### Texto ilegible

**Causas comunes:**

1. **Colores de texto no configurados**
   - Solución: Añadir `typography.colors` en store-config.json

2. **Contraste insuficiente**
   - Solución: Elegir colores con mayor diferencia de luminosidad

3. **Fondo y texto del mismo color**
   - Solución: Verificar que `getContrastingForeground()` funciona correctamente

---

## Recursos Adicionales

- [OKLCH Color Picker](https://oklch.com/)
- [Tailwind CSS Colors](https://tailwindcss.com/docs/customizing-colors)
- [shadcn/ui Components](https://ui.shadcn.com/)
- [WCAG Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

---

## Conclusión

El sistema de estilos del template está diseñado para:

✅ Ser completamente configurable sin tocar código
✅ Garantizar legibilidad automática
✅ Mantener consistencia visual
✅ Permitir personalización total
✅ Seguir estándares de accesibilidad

Para dudas o problemas, consulta los archivos:
- `lib/color-utils.ts` (lógica de conversión)
- `components/dynamic-color-provider.tsx` (aplicación de colores)
- `app/globals.css` (variables CSS base)
