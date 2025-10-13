# MIDDAS E-commerce Template

## Propósito del Proyecto

Este es un template de e-commerce moderno y completamente configurable, diseñado para funcionar como punto de partida para crear tiendas online personalizables. El template se integra con el sistema MIDDAS (POS y gestión de inventario) y permite que cada comercio tenga su propia tienda online con identidad visual única.

### Características Principales

- **Totalmente Configurable**: Todo el aspecto visual, textos y configuraciones se manejan desde un archivo JSON
- **Temas Dinámicos**: Paletas de colores y tipografías configurables en tiempo real
- **Responsive**: Diseño optimizado para dispositivos móviles, tablets y desktop
- **Integración con Backend**: Conexión directa con API de MIDDAS para productos, inventario y pedidos
- **SEO Optimizado**: Metadatos configurables para mejor posicionamiento
- **Carrito de Compras**: Funcionalidad completa de e-commerce
- **WhatsApp Integration**: Pedidos directos por WhatsApp

---

## Estructura del Proyecto

```
middas-ecommerce-template1/
├── app/                          # App Router de Next.js 15
│   ├── page.tsx                  # Página principal (hero + productos destacados)
│   ├── productos/page.tsx        # Catálogo completo de productos
│   ├── layout.tsx                # Layout principal con providers
│   └── globals.css               # Estilos globales y variables CSS
│
├── components/                   # Componentes React reutilizables
│   ├── ui/                       # Componentes UI base (shadcn/ui)
│   ├── header.tsx                # Header con logo y navegación
│   ├── footer.tsx                # Footer con info de contacto
│   ├── product-card.tsx          # Tarjeta de producto
│   ├── cart-sidebar.tsx          # Sidebar del carrito de compras
│   ├── dynamic-color-provider.tsx # Provider para colores dinámicos
│   └── theme-provider.tsx        # Provider de temas (next-themes)
│
├── contexts/                     # Context API para estado global
│   └── cart-context.tsx          # Estado del carrito de compras
│
├── hooks/                        # Custom React Hooks
│   └── use-mobile.tsx            # Hook para detectar dispositivos móviles
│
├── lib/                          # Utilidades y helpers
│   ├── store-config.ts           # Types y función para leer store-config.json
│   ├── font-schemes.ts           # Configuración de esquemas de fuentes
│   ├── color-utils.ts            # Conversión de colores HEX a OKLCH
│   ├── api.ts                    # Cliente API para backend MIDDAS
│   └── utils.ts                  # Utilidades generales (cn, etc)
│
├── public/
│   └── config/
│       └── store-config.json     # ⭐ ARCHIVO DE CONFIGURACIÓN PRINCIPAL
│
├── docs/                         # Documentación del proyecto
│   ├── README.md                 # Este archivo
│   └── ESTILOS.md                # Guía de estilos y colores
│
└── styles/
    └── globals.css               # Estilos CSS globales (deprecado, usar app/globals.css)
```

---

## Archivo de Configuración Principal

El archivo `public/config/store-config.json` es el corazón del sistema de configuración. Contiene toda la información que personaliza la tienda:

### Estructura del store-config.json

```json
{
  "storeId": "...",              // ID de la tienda en MIDDAS
  "apiUrl": "...",               // URL del backend API

  "business": {                  // Información del negocio
    "name": "...",
    "description": "...",
    "address": "...",
    "phone": "...",
    "whatsapp": "...",
    "email": "...",
    "socialMedia": {
      "instagram": "...",
      "facebook": "..."
    }
  },

  "branding": {                  // Identidad visual
    "logo": "...",               // URL del logo
    "banner": "...",             // URL del banner principal
    "bannerTitle": "...",        // Título del hero
    "bannerSubtitle": "...",     // Subtítulo del hero

    "colorScheme": {             // Paleta de colores
      "name": "fresh",
      "primary": "#0066CC",      // Color principal (botones, links)
      "secondary": "#FFFFFF",    // Color secundario
      "accent": "#00B4D8",       // Color de acento
      "background": "#F0F7FF"    // Color de fondo
    },

    "typography": {              // Configuración de tipografía
      "fontScheme": "modern",    // elegant | modern | classic
      "colors": {                // Colores de texto (opcional)
        "heading": "#1a1a1a",    // Color de títulos
        "body": "#2d2d2d",       // Color de texto normal
        "muted": "#6b7280"       // Color de texto secundario
      }
    }
  },

  "settings": {                  // Configuraciones funcionales
    "showStock": true,
    "allowOrders": true,
    "orderMethod": "whatsapp",
    "showPrices": true,
    "currency": "ARS",
    "currencySymbol": "$"
  },

  "seo": {                       // SEO y metadatos
    "title": "...",
    "description": "...",
    "keywords": []
  },

  "analytics": {}                // Configuración de analytics (futuro)
}
```

---

## Archivos Clave del Proyecto

### 1. `app/layout.tsx`
- Layout principal de la aplicación
- Carga el `store-config.json` en el servidor
- Aplica el esquema de fuentes
- Integra los providers (Cart, DynamicColor, Theme)
- Genera metadatos para SEO

### 2. `lib/store-config.ts`
- Define el tipo `StoreConfig` con TypeScript
- Función `getStoreConfig()` para leer la configuración
- Funciona tanto en servidor (SSR) como en cliente

### 3. `lib/color-utils.ts`
- Convierte colores HEX a formato OKLCH
- Calcula contraste automático (texto blanco en fondos oscuros)
- Genera paleta completa de colores desde store-config
- Función `generateColorPalette()` con valores por defecto

### 4. `components/dynamic-color-provider.tsx`
- Componente cliente que inyecta CSS dinámico
- Lee los colores del store-config
- Crea variables CSS personalizadas en runtime
- Sobrescribe valores por defecto con `!important`

### 5. `lib/font-schemes.ts`
- Define tres esquemas de fuentes: elegant, modern, classic
- Cada esquema tiene fonts para body y headings
- Usa Google Fonts (Playfair Display, Inter, Lora, etc.)

### 6. `lib/api.ts`
- Cliente HTTP para comunicarse con backend MIDDAS
- Funciones para obtener productos destacados y listados
- Maneja errores de conexión y respuestas

---

## Datos Importantes a Tener en Cuenta

### Sistema de Colores Dinámicos

1. Los colores se definen en formato **HEX** en `store-config.json`
2. Se convierten automáticamente a **OKLCH** (formato moderno usado por Tailwind CSS v4)
3. El sistema calcula automáticamente el contraste para texto legible
4. Los botones con fondo oscuro tendrán texto blanco automáticamente
5. El texto sobre fondos claros será oscuro por defecto

### Tipografía

- **Tres esquemas disponibles**: elegant, modern, classic
- Cada esquema carga fuentes específicas de Google Fonts
- Los headings (`<h1>` a `<h6>`) usan la fuente de títulos
- El body text usa la fuente de cuerpo
- Los colores de texto son configurables o usan defaults oscuros

### Carrito de Compras

- Estado global manejado por `CartContext`
- Persiste en localStorage del navegador
- Funcionalidad completa: agregar, eliminar, cambiar cantidad
- Sidebar deslizable con resumen del pedido
- Envío de pedidos por WhatsApp con formato estructurado

### Integración con Backend

- El template se conecta al backend MIDDAS vía API REST
- Requiere `storeId` válido para obtener productos
- Las imágenes de productos vienen desde S3 (URLs públicas)
- Los productos se filtran por tienda usando el `userId`

### Responsive Design

- Mobile-first approach
- Breakpoints: sm (640px), md (768px), lg (1024px), xl (1280px)
- Header con menú hamburguesa en móvil
- Grid adaptativo de productos (1 col móvil, 2 tablet, 4 desktop)

### Despliegue

- Optimizado para Vercel con `output: 'standalone'`
- Las imágenes se sirven vía CDN (S3)
- El `store-config.json` se lee en build time para SSR
- También se puede leer en cliente para actualizaciones dinámicas

---

## Comandos Principales

```bash
# Instalar dependencias
npm install

# Desarrollo local
npm run dev

# Build para producción
npm run build

# Iniciar en producción
npm start

# Linting
npm run lint
```

---

## Variables de Entorno

El template no requiere variables de entorno (todo está en `store-config.json`), pero opcionalmente se pueden usar:

```env
# .env.local (opcional)
NEXT_PUBLIC_API_URL=http://localhost:5000
NEXT_PUBLIC_STORE_ID=68e03cde5f3018eaef75750b
```

---

## Extensibilidad

### Agregar Nuevas Paletas de Colores

1. Edita `store-config.json` → `branding.colorScheme`
2. Define primary, secondary, accent, background
3. Los colores de texto se calculan automáticamente

### Agregar Nuevos Esquemas de Fuentes

1. Edita `lib/font-schemes.ts`
2. Agrega un nuevo esquema con body y heading fonts
3. Importa las fuentes desde `next/font/google`
4. Actualiza el tipo en `store-config.ts`

### Personalizar Componentes UI

Los componentes base están en `components/ui/` usando shadcn/ui:
- `Button`, `Card`, `Input`, `Sheet`, `Badge`, etc.
- Siguen la filosofía de "copy-paste" (no son npm packages)
- Totalmente personalizables

---

## Soporte y Contribuciones

Este template es parte del sistema MIDDAS. Para reportar problemas o sugerencias, contacta al equipo de desarrollo.

**Stack Tecnológico:**
- Next.js 15 (App Router)
- React 19
- TypeScript
- Tailwind CSS v4
- shadcn/ui
- Lucide Icons
- Vercel Analytics
