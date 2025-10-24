ver documentación en:
https://github.com/elan-sk/core-tailwidcss.git

# Guía Completa del Core CSS Tailwind

**Slug:** `guia-core-css-tailwind`
**Categoría:** Desarrollo Frontend
**Última actualización:** Octubre 2025

---

## 📋 Tabla de Contenidos

1. Introducción y Arquitectura
2. Sistema de Capas de Tailwind
3. Sistema de Colores Semánticos
4. Tipografía Responsive con Clamp
5. Sistema de Containers Bootstrap-like
6. Utilidades de Flexbox Avanzadas
7. Sistema de Posicionamiento Absoluto
8. Utilidades de Tamaño con Conversión Automática
9. Sistema de Botones con Pseudo-elementos
10. Herramientas de Debugging
11. Fuentes Personalizadas

---

## 🏗️ Introducción y Arquitectura

### Estructura del Proyecto

El core está organizado en módulos independientes que se importan en archivos `index.css`:

```
styles/
├── index.css              → Archivo principal que importa todo
├── bases/
│   ├── index.css          → Importa _base.css
│   └── _base.css          → Estilos base del HTML
├── settings/
│   ├── index.css          → Importa colores, containers, fonts, typography
│   ├── _colors.css        → Sistema de colores semánticos
│   ├── _containers.css    → Sistema de containers responsive
│   ├── _fonts.css         → Importación de fuentes
│   └── _typography.css    → Escalas tipográficas
├── atoms/
│   ├── index.css          → Importa _buttons.css
│   └── _buttons.css       → Componentes de botones
├── utilities/
│   ├── index.css          → Importa utilidades varias
│   ├── _decorateds.css    → Decoraciones (scrollbar, imágenes)
│   ├── _flex.css          → Utilidades de flexbox
│   ├── _positions.css     → Sistema de posicionamiento
│   ├── _sizes-rem.css     → Conversión px → rem
│   └── _test.css          → Herramientas de debug
├── libraries/
│   ├── index.css          → Importa _swiper.css
│   └── _swiper.css        → Estilos de Swiper.js
└── readme                 → Documentación del core

```

### Filosofía del Core

Este core extiende Tailwind CSS con:

1. **Variables CSS (@theme)** - Define tokens de diseño reutilizables
2. **Utilidades personalizadas (@utility)** - Crea clases nuevas que no existen en Tailwind
3. **Capas organizadas (@layer)** - Controla la especificidad y el orden de los estilos
4. **Componentes reutilizables** - Patrones comunes preconfigurados

---

## 🎨 Sistema de Capas de Tailwind

### ¿Qué son las capas?

Tailwind organiza el CSS en tres capas principales:

```css
@layer base {
  /* Estilos base del HTML (h1, body, etc.) */
}

@layer components {
  /* Componentes reutilizables (.btn-primary, .card, etc.) */
}

@layer utilities {
  /* Utilidades de una sola responsabilidad (.flex-center, etc.) */
}
```

### Orden de Especificidad

```
base < components < utilities < estilos inline
```

Esto significa que:
- Las **utilidades** siempre ganan sobre **components**
- Los **components** ganan sobre **base**
- Puedes sobrescribir cualquier cosa con clases de Tailwind

### Ejemplo Práctico

```css
/* En _base.css */
@layer base {
  a { @apply text-button }  /* Todos los enlaces tamaño button */
}

/* Puedes sobrescribirlo con utilidades */
<a class="text-h3">Este enlace es más grande</a>
```

---

## 🎨 Sistema de Colores Semánticos

### Arquitectura del Sistema

El sistema de colores tiene **dos partes fundamentales**:

#### 1. Definición de Variables (@theme)

```css
@theme {
  /* Color de fondo */
  --color-primary: #f6993f;

  /* Color de texto que va SOBRE ese fondo */
  --color-on-primary: #ffffff;
}
```

**¿Por qué @theme?**
- Define variables CSS globales
- Tailwind las convierte automáticamente en clases: `bg-primary`, `text-on-primary`
- Se puede acceder con `var(--color-primary)` en CSS personalizado

#### 2. Aplicación Automática (@layer base)

```css
@layer base {
  .bg-primary {
    @apply text-on-primary  /* ⬅️ Magia aquí */
  }
}
```

**¿Qué hace esto?**
Cuando aplicas `bg-primary` a un elemento, **automáticamente** también aplica `text-on-primary` al texto.

### Anatomía de un Color Completo

Veamos cómo funciona el color primario:

```css
/* 1. DEFINIMOS LAS VARIABLES */
@theme {
  --color-primary-dk: #e3342f;    /* Primario oscuro */
  --color-primary: #f6993f;       /* Primario normal */
  --color-primary-lt: #ffed4a;    /* Primario claro */

  /* Textos que van sobre cada uno */
  --color-on-primary-dk: #ffffff; /* Blanco sobre oscuro */
  --color-on-primary: #ffffff;    /* Blanco sobre normal */
  --color-on-primary-lt: #1f2937; /* Oscuro sobre claro */
}

/* 2. CONECTAMOS FONDO CON TEXTO */
@layer base {
  .bg-primary-dk { @apply text-on-primary-dk }
  .bg-primary { @apply text-on-primary }
  .bg-primary-lt { @apply text-on-primary-lt }
}
```

### ¿Por Qué Este Sistema?

**Problema anterior:**
```html
<button class="bg-orange-500 text-white">
  Acción
</button>
```

Si cambias `bg-orange-500` a `bg-blue-500`, debes también cambiar manualmente `text-white` a `text-gray-900` (porque el azul claro necesita texto oscuro).

**Solución actual:**
```html
<button class="bg-primary">
  Acción
</button>
```

El texto **se ajusta automáticamente** según el fondo. Si cambias el valor de `--color-primary` de naranja a azul claro, también cambias `--color-on-primary` de blanco a oscuro, ¡y todos los componentes se actualizan!

### Nomenclatura Semántica

```
[rol]-[variante]
  ↓       ↓
primary   dk (dark)
          (normal)
          lt (light)
```

**Roles disponibles:**
- `primary` → Acciones principales, CTAs
- `secondary` → Acciones secundarias
- `tertiary` → Acentos, decoración
- `background` → Fondo principal del sitio
- `surface` → Fondos de tarjetas/secciones
- `outline` → Bordes y divisores
- `success/info/error` → Mensajes de estado

### Paleta Completa Explicada

```css
@theme {
  /*=== PRIMARIOS ===*/
  /* Usa estos para botones principales, headers, CTAs */
  --color-primary-dk: #e3342f;
  --color-primary: #f6993f;
  --color-primary-lt: #ffed4a;

  /*=== SECUNDARIOS ===*/
  /* Usa estos para acciones secundarias, info adicional */
  --color-secondary-dk: #38c172;
  --color-secondary: #4dc0b5;
  --color-secondary-lt: #3490dc;

  /*=== TERCIARIOS ===*/
  /* Usa estos para acentos, decoración, elementos menores */
  --color-tertiary-dk: #6574cd;
  --color-tertiary: #9561e2;
  --color-tertiary-lt: #f66d9b;

  /*=== FONDOS ===*/
  --color-background: #f8fafc;  /* Fondo del body */
  --color-surface: #f9f3ef;     /* Tarjetas, cards */
  --color-outline: #f66d9b;     /* Bordes */

  /*=== MENSAJES ===*/
  --color-success: #22c55e;     /* Operación exitosa */
  --color-info: #94a3b8;        /* Información neutral */
  --color-error: #ef4444;       /* Errores, alertas */
}
```

### Ejemplos de Uso Real

**Tarjeta con colores automáticos:**
```html
<article class="bg-surface p-6 rounded-lg">
  <!-- ⬆️ bg-surface aplica automáticamente text-on-surface -->

  <h3 class="text-h3 text-primary mb-4">
    Título en color primario
  </h3>

  <p class="text-base">
    Este párrafo hereda text-on-surface automáticamente.
    No necesité especificar el color del texto.
  </p>

  <button class="bg-primary px-6 py-2 rounded-full">
    <!-- ⬆️ bg-primary aplica automáticamente text-on-primary -->
    Acción
  </button>
</article>
```

**Hero section que cambia de tema:**
```html
<!-- Tema claro (texto oscuro automático) -->
<section class="bg-primary-lt py-16">
  <h1 class="text-h1">Título</h1>
  <p>Texto automáticamente oscuro (#1f2937)</p>
</section>

<!-- Tema oscuro (texto blanco automático) -->
<section class="bg-primary-dk py-16">
  <h1 class="text-h1">Título</h1>
  <p>Texto automáticamente blanco (#ffffff)</p>
</section>
```

---

## 📝 Tipografía Responsive con Clamp

### El Problema que Resuelve

**Antes (con media queries):**
```css
.text-h1 {
  font-size: 32px;
}

@media (min-width: 768px) {
  .text-h1 { font-size: 40px; }
}

@media (min-width: 1200px) {
  .text-h1 { font-size: 48px; }
}
```

❌ Saltos bruscos entre breakpoints
❌ Mucho código
❌ Difícil de mantener

**Ahora (con clamp):**
```css
.text-h1 {
  font-size: clamp(2.55rem, 2.4rem + 0.686vw, 3rem);
}
```

✅ Transición suave
✅ Una sola línea
✅ Se adapta a cualquier pantalla

### Anatomía de clamp()

```css
clamp(MIN, PREFERIDO, MAX)
       ↓        ↓        ↓
    2.55rem   2.4rem   3rem
             + 0.686vw
```

- **MIN (2.55rem = 41px)**: Tamaño mínimo en pantallas pequeñas
- **PREFERIDO (2.4rem + 0.686vw)**: Fórmula que crece con el viewport
- **MAX (3rem = 48px)**: Tamaño máximo en pantallas grandes

### Cómo Funciona la Parte Variable

La parte `2.4rem + 0.686vw` significa:

- `2.4rem` es la base (38.4px)
- `0.686vw` crece proporcionalmente al ancho del viewport
- En un viewport de 1400px: `0.686vw = 9.6px`
- Total: `38.4px + 9.6px = 48px` (tope máximo)

### Implementación en el Core

```css
@theme {
  /* Cada tamaño es una función clamp() */
  --text-h1: clamp(2.55rem, 2.4rem + 0.686vw, 3rem);     /* 41-48px */
  --text-h2: clamp(2.125rem, 2rem + 0.571vw, 2.5rem);    /* 34-40px */
  --text-h3: clamp(1.7rem, 1.6rem + 0.457vw, 2rem);      /* 27-32px */
  --text-h4: clamp(1.488rem, 1.4rem + 0.4vw, 1.75rem);   /* 24-28px */
  --text-header: clamp(1.275rem, 1.2rem + 0.343vw, 1.5rem); /* 20-24px */
  --text-button: clamp(0.956rem, 0.9rem + 0.257vw, 1.125rem); /* 15-18px */
  --text-base: clamp(0.903rem, 0.85rem + 0.243vw, 1.063rem); /* 14-17px */
  --text-small: clamp(0.797rem, 0.75rem + 0.214vw, 0.938rem); /* 13-15px */
}

/* Creamos las clases de utilidad */
@utility text-h1 { font-size: var(--text-h1); }
@utility text-h2 { font-size: var(--text-h2); }
/* ... etc */
```

### Estilos Automáticos por Tipo

El core aplica estilos predefinidos según el tipo de texto:

```css
@layer base {
  /* TÍTULOS */
  :is(.text-h1, .text-h2, .text-h3, .text-h4) {
    @apply font-serif        /* Merriweather */
           text-balance      /* Balancea líneas */
           font-bold         /* Negritas */
           leading-tight;    /* Interlineado ajustado */
  }

  /* HEADER Y BOTONES */
  .text-header,
  .text-button {
    @apply font-sans         /* OpenSans */
           font-semibold     /* Semi-negritas */
           text-balance;     /* Balancea líneas */
  }

  /* TEXTO NORMAL */
  :is(.text-base, .text-small) {
    @apply font-sans         /* OpenSans */
           text-pretty       /* Evita palabras huérfanas */
           leading-normal;   /* Interlineado normal */
  }
}
```

### 🛠️ Crear Nuevos Tamaños

**Herramienta:** [Calculadora Clamp CSS](https://elan-sk.github.io/calculadora-clamp-css/)

**Paso a paso:**

1. **Define tus tamaños objetivo:**
   - Móvil (350px): `14px`
   - Desktop (1400px): `20px`

2. **Ingresa en la calculadora:**
   - Min font size: `14`
   - Max font size: `20`
   - Min viewport: `350`
   - Max viewport: `1400`

3. **Obtén el resultado:**
   ```css
   clamp(0.875rem, 0.7857rem + 0.3571vw, 1.25rem)
   ```

4. **Agrégalo al core:**
   ```css
   @theme {
     --text-custom: clamp(0.875rem, 0.7857rem + 0.3571vw, 1.25rem);
   }

   @utility text-custom {
     font-size: var(--text-custom);
   }
   ```

### Familias Tipográficas

```css
@theme {
  --font-serif: 'Merriweather', serif;  /* Títulos */
  --font-sans: 'OpenSans', sans-serif;  /* Cuerpo */
  --font-mono: 'RobotoMono', monospace; /* Código */
}
```

**Uso:**
```html
<h1 class="font-serif">Título elegante</h1>
<p class="font-sans">Párrafo legible</p>
<code class="font-mono">console.log('código')</code>
```

---

## 📦 Sistema de Containers Bootstrap-like

### Filosofía

Bootstrap usa containers con `max-width` fijos por breakpoint. Este core **replica ese sistema** pero usando la sintaxis de Tailwind.

### Breakpoints Definidos

```css
@theme {
  --breakpoint-xs: 350px;
  --breakpoint-sm: 576px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 992px;
  --breakpoint-xl: 1200px;
  --breakpoint-2xl: 1400px;

  /* Max-widths por breakpoint */
  --container-sm: 540px;
  --container-md: 720px;
  --container-lg: 960px;
  --container-xl: 1140px;
  --container-2xl: 1320px;
}
```

### Anatomía del Container Principal

```css
@utility container {
  @apply w-[90%] mx-auto px-4;  /* Base: 90% ancho, centrado */

  /* En cada breakpoint, aplica el max-width correspondiente */
  @media (width < 576px) {
    max-width: var(--container-sm);  /* 540px */
  }
  @media (width >= 576px) {
    max-width: var(--container-sm);  /* 540px */
  }
  @media (width >= 768px) {
    max-width: var(--container-md);  /* 720px */
  }
  @media (width >= 992px) {
    max-width: var(--container-lg);  /* 960px */
  }
  @media (width >= 1200px) {
    max-width: var(--container-xl);  /* 1140px */
  }
  @media (width >= 1400px) {
    max-width: var(--container-2xl); /* 1320px */
  }
}
```

**¿Qué hace esto?**

1. El contenedor ocupa el **90% del ancho** de su padre
2. Se **centra horizontalmente** con `mx-auto`
3. Tiene **padding lateral** de `1rem` (`px-4`)
4. El **max-width** cambia según el tamaño de pantalla

### Containers Específicos

```css
/* Container pequeño: solo crece hasta 540px */
@utility container-sm {
  @apply w-[90%] mx-auto px-4;
  @media (width >= 576px) {
    max-width: var(--container-sm);
  }
}

/* Container mediano: crece hasta 720px */
@utility container-md {
  @apply w-[90%] mx-auto px-4;
  @media (width >= 576px) { max-width: var(--container-sm); }
  @media (width >= 768px) { max-width: var(--container-md); }
}

/* ... y así sucesivamente hasta container-2xl */
```

### Container Full Width

```css
@utility container-full {
  @apply w-full mx-auto px-4;
}
```

No tiene `max-width`, ocupa siempre el 100% del ancho disponible.

### Ejemplos Visuales

```html
<!-- Layout típico de página -->

<!-- Hero: ancho completo -->
<section class="bg-primary">
  <div class="container-full py-20">
    <h1>Hero de ancho completo</h1>
  </div>
</section>

<!-- Contenido: ancho adaptable estándar -->
<section class="bg-background">
  <div class="container py-16">
    <p>Se adapta a todos los breakpoints</p>
  </div>
</section>

<!-- Texto: ancho más estrecho para legibilidad -->
<article class="bg-surface">
  <div class="container-md py-12">
    <p>Texto más cómodo de leer (máx 720px)</p>
  </div>
</article>
```

### Tabla de Comparación

| Clase | Móvil | SM (576px) | MD (768px) | LG (992px) | XL (1200px) | 2XL (1400px) |
|-------|-------|-----------|-----------|-----------|-----------|-------------|
| `container-full` | 100% | 100% | 100% | 100% | 100% | 100% |
| `container-sm` | 90% | 540px | 540px | 540px | 540px | 540px |
| `container-md` | 90% | 540px | 720px | 720px | 720px | 720px |
| `container-lg` | 90% | 540px | 720px | 960px | 960px | 960px |
| `container-xl` | 90% | 540px | 720px | 960px | 1140px | 1140px |
| `container-2xl` | 90% | 540px | 720px | 960px | 1140px | 1320px |
| `container` | 90% | 540px | 720px | 960px | 1140px | 1320px |

---

## 🔲 Utilidades de Flexbox Avanzadas

### 1. Flex Center

**Problema común:**
```css
/* Código repetitivo */
.elemento {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

**Solución del core:**
```css
@utility flex-center {
  @apply flex justify-center items-center;
}
```

**Uso:**
```html
<div class="flex-center h-screen">
  <h1>Perfectamente centrado</h1>
</div>
```

### 2. Flex Container (Columna con Gap)

**¿Qué hace?**
Organiza elementos en **columna vertical** con espaciado opcional.

**Implementación:**
```css
/* Sin gap */
@utility flex-container {
  @apply flex flex-col;
}

/* Con gap usando escala de Tailwind */
@utility flex-container-* {
  @apply flex flex-col;
  gap: calc(var(--spacing) * --value(integer));
}

/* Con gap en píxeles */
@utility flex-container-px-* {
  @apply flex flex-col;
  gap: calc(--value(number) / 16 * 1rem);
}
```

**¿Cómo funciona `--value(integer)`?**

Es una función especial de Tailwind v4 que toma el número en la clase:

```html
<!-- flex-container-4 -->
<!-- --value(integer) = 4 -->
<!-- gap = 4 * var(--spacing) = 4 * 0.25rem = 1rem -->
<div class="flex-container-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <!-- ⬆️ gap de 1rem entre ellos -->
</div>

<!-- flex-container-px-24 -->
<!-- --value(number) = 24 -->
<!-- gap = 24 / 16 * 1rem = 1.5rem -->
<div class="flex-container-px-24">
  <div>Item 1</div>
  <div>Item 2</div>
  <!-- ⬆️ gap de 24px (1.5rem) entre ellos -->
</div>
```

### 3. Flex Grid (Sistema de Grid con Flexbox)

**Filosofía:**
Reemplazar el sistema de `.row` y `.col-*` de Bootstrap, pero más flexible.

**Implementación base:**
```css
@utility flex-grid {
  @apply flex flex-wrap;
  & > * {
    flex: 0 0 100%;  /* Todos los hijos ocupan 100% */
  }
}
```

**¿Qué hace `flex: 0 0 100%`?**

```
flex: flex-grow flex-shrink flex-basis
      ↓          ↓          ↓
      0          0          100%
```

- `flex-grow: 0` → No crece más allá de su tamaño base
- `flex-shrink: 0` → No se encoge menos de su tamaño base
- `flex-basis: 100%` → Tamaño base es 100% del contenedor

**Grid con columnas específicas:**
```css
@utility flex-grid-3 {
  @apply flex flex-wrap;
  & > * {
    flex: 0 1 calc(1 / 3 * 100%);  /* 33.33% cada uno */
  }
}
```

**¿Por qué `flex-shrink: 1` aquí?**

Permite que los elementos se encojan si el espacio es insuficiente, evitando overflow.

**⚠️ IMPORTANTE: Uso de Gap en flex-grid**

Cuando uses `flex-grid-*`, debes tener **mucho cuidado con el gap**:

- ❌ **NO uses `gap-*`** (gap en ambos ejes) porque hará que los elementos no quepan y se muevan a la siguiente línea
- ✅ **USA `gap-y-*` o `gap-y-px-*`** (solo gap vertical) para espaciar las filas

**¿Por qué?**

El `flex-grid-3` calcula que cada elemento ocupe exactamente `33.33%` del ancho. Si agregas `gap` horizontal, los elementos ocuparán `33.33% + gap`, excediendo el 100% y rompiendo el layout (igual que pasa con `.row` y `gap` en Bootstrap).

**Uso correcto:**
```html
<!-- ✅ CORRECTO: gap solo vertical -->
<div class="flex-grid-3 gap-y-4">
  <div>Columna 1</div>
  <div>Columna 2</div>
  <div>Columna 3</div>
  <div>Columna 4</div>
  <div>Columna 5</div>
  <div>Columna 6</div>
</div>

<!-- ✅ CORRECTO: gap en píxeles vertical -->
<div class="flex-grid-3 gap-y-px-24">
  <div>Columna 1</div>
  <div>Columna 2</div>
  <div>Columna 3</div>
</div>

<!-- ❌ INCORRECTO: gap en ambos ejes -->
<div class="flex-grid-3 gap-4">
  <div>Columna 1</div>
  <div>Columna 2</div>
  <div>Columna 3</div>
  <!-- Los elementos se moverán a la siguiente línea -->
</div>

<!-- Grid responsive -->
<div class="flex-grid-2 md:flex-grid-3 lg:flex-grid-4 gap-y-6">
  <div>Card 1</div>
  <div>Card 2</div>
  <div>Card 3</div>
  <div>Card 4</div>
</div>
```

**Alternativa para gap horizontal:**

Si necesitas espaciado horizontal, usa `padding` en los elementos hijos:

```html
<div class="flex-grid-3 gap-y-4">
  <div class="px-2">
    <div class="bg-surface p-4">Contenido</div>
  </div>
  <div class="px-2">
    <div class="bg-surface p-4">Contenido</div>
  </div>
  <div class="px-2">
    <div class="bg-surface p-4">Contenido</div>
  </div>
</div>
```

### 4. Personalizando Hijos con `basis`

```html
<div class="flex-grid-4 gap-4">
  <!-- Hijo normal: 25% -->
  <div>Normal</div>

  <!-- Hijo que ocupa 50% -->
  <div class="basis-1/2">Grande</div>

  <!-- Hijo que ocupa 100% (nueva línea) -->
  <div class="basis-full">Ancho completo</div>

  <!-- Hijo que ocupa 75% -->
  <div class="basis-3/4">Muy grande</div>
</div>
```

**¿Qué hace `basis-1/2`?**

Sobrescribe el `flex-basis` del padre:
```css
.basis-1/2 { flex-basis: 50%; }
```

### Grid Dinámico con `--value`

```css
@utility flex-grid-* {
  @apply flex flex-wrap;
  & > * {
    flex: 0 1 calc(1 / --value(integer) * 100%);
  }
}
```

**Uso:**
```html
<!-- flex-grid-5 = 5 columnas -->
<div class="flex-grid-5">
  <div>1</div>
  <div>2</div>
  <div>3</div>
  <div>4</div>
  <div>5</div>
</div>

<!-- flex-grid-8 = 8 columnas -->
<div class="flex-grid-8 gap-2">
  <!-- 8 elementos por fila -->
</div>
```

---

## 📍 Sistema de Posicionamiento Absoluto

### Filosofía

Posicionar elementos con `position: absolute` requiere escribir código repetitivo. Este sistema lo simplifica con clases semánticas.

### Requisito Importante

**El padre debe estar posicionado:**
```html
<div class="relative">  <!-- ⬅️ Necesario -->
  <div class="position-center">
    Hijo posicionado
  </div>
</div>
```

### 1. Position Full (Cubre todo)

```css
@utility position-full {
  @apply absolute inset-0;
}
```

**¿Qué hace `inset-0`?**
```css
inset: 0;  /* Shorthand para: */
top: 0;
right: 0;
bottom: 0;
left: 0;
```

**Uso:**
```html
<div class="relative h-64">
  <!-- Cubre todo el padre -->
  <div class="position-full bg-primary/20">
    Overlay
  </div>
</div>
```

### 2. Centrados Perfectos

**Centro absoluto (X e Y):**
```css
@utility position-center {
  @apply absolute
         top-1/2        /* 50% desde arriba */
         left-1/2       /* 50% desde izquierda */
         -translate-1/2; /* Corrige el desplazamiento */
}
```

**¿Por qué `-translate-1/2`?**

`top-1/2` y `left-1/2` mueven la **esquina superior izquierda** al centro. Para centrar el **centro del elemento**, necesitamos moverlo hacia atrás:

```css
-translate-1/2 = transform: translate(-50%, -50%);
```

**Solo centrado horizontal:**
```css
@utility position-x-center {
  @apply absolute
         left-1/2
         -translate-x-1/2;
}
```

**Solo centrado vertical:**
```css
@utility position-y-center {
  @apply absolute
         top-1/2
         -translate-y-1/2;
}
```

**Uso:**
```html
<div class="relative h-96 bg-surface">
  <!-- Centro absoluto -->
  <div class="position-center">
    <h2 class="text-h2">Centrado perfecto</h2>
  </div>

  <!-- Solo horizontal -->
  <div class="position-x-center top-4">
    <p>Top centrado horizontalmente</p>
  </div>
</div>
```

### 3. Esquinas y Bordes

**Esquinas:**
```css
/* Superior izquierda */
@utility position-tl {
  @apply absolute top-0 left-0;
}

/* Superior derecha */
@utility position-tr {
  @apply absolute top-0 right-0;
}

/* Inferior izquierda */
@utility position-bl {
  @apply absolute bottom-0 left-0;
}

/* Inferior derecha */
@utility position-br {
  @apply absolute bottom-0 right-0;
}
```

**Bordes completos:**
```css
/* Borde superior completo */
@utility position-t {
  @apply absolute top-0 left-0 right-0;
}

/* Borde inferior completo */
@utility position-b {
  @apply absolute bottom-0 left-0 right-0;
}

/* Borde izquierdo completo */
@utility position-l {
  @apply absolute top-0 bottom-0 left-0;
}

/* Borde derecho completo */
@utility position-r {
  @apply absolute top-0 bottom-0 right-0;
}
```

**Uso práctico:**
```html
<div class="relative h-96 bg-surface rounded-lg overflow-hidden">
  <!-- Botón cerrar en esquina superior derecha -->
  <button class="position-tr p-4 text-h4">✕</button>

  <!-- Badge en esquina superior izquierda -->
  <span class="position-tl bg-error px-3 py-1 text-small">
    Nuevo
  </span>

  <!-- Barra de navegación superior -->
  <nav class="position-t bg-primary h-16 flex-center">
    Navigation
  </nav>

  <!-- Footer inferior -->
  <footer class="position-b bg-surface p-4">
    Footer
  </footer>
</div>
```

### 4. Centrados por Lado

```css
/* Centrado en el borde superior */
@utility position-t-center {
  @apply absolute
         top-0
         left-1/2
         -translate-x-1/2;
}

/* Centrado en el borde inferior */
@utility position-b-center {
  @apply absolute
         bottom-0
         left-1/2
         -translate-x-1/2;
}

/* Centrado en el borde izquierdo */
@utility position-l-center {
  @apply absolute
         left-0
         top-1/2
         -translate-y-1/2;
}

/* Centrado en el borde derecho */
@utility position-r-center {
  @apply absolute
         right-0
         top-1/2
         -translate-y-1/2;
}
```

**Ejemplo visual:**
```html
<div class="relative h-96 bg-surface rounded-lg">
  <!-- Badge centrado arriba -->
  <div class="position-t-center bg-primary px-4 py-2 rounded-b-lg">
    Top Badge
  </div>

  <!-- Badge centrado abajo -->
  <div class="position-b-center bg-secondary px-4 py-2 rounded-t-lg">
    Bottom Badge
  </div>

  <!-- Badge centrado izquierda -->
  <div class="position-l-center bg-tertiary px-2 py-4 rounded-r-lg">
    Left
  </div>

  <!-- Badge centrado derecha -->
  <div class="position-r-center bg-success px-2 py-4 rounded-l-lg">
    Right
  </div>
</div>
```

### Diagrama de Posiciones

```
position-tl ──────── position-t-center ──────── position-tr
     │                    │                        │
     │           position-t (todo el top)          │
     │                    │                        │
     │                    │                        │
position-l-center ─── position-center ─── position-r-center
     │                    │                        │
     │                    │                        │
     │           position-b (todo el bottom)       │
     │                    │                        │
position-bl ──────── position-b-center ─────── position-br
```

---

## 📏 Utilidades de Tamaño con Conversión Automática

### El Problema

Tailwind usa una escala arbitraria (`w-4 = 1rem = 16px`), pero a veces necesitas **valores exactos en píxeles** que se conviertan a `rem` automáticamente.

### La Solución

El core incluye utilidades que convierten píxeles a `rem` al vuelo:

```css
@utility w-px-* {
  width: calc(--value(number) / 16 * 1rem);
}
```

**¿Cómo funciona?**

```html
<div class="w-px-320">
  <!-- --value(number) = 320 -->
  <!-- width = 320 / 16 * 1rem = 20rem -->
</div>
```

### 1. Width y Height

```css
/* Tamaño combinado (cuadrado) */
@utility size-px-* {
  height: calc(--value(number) / 16 * 1rem);
  width: calc(--value(number) / 16 * 1rem);
}

/* Width */
@utility w-px-* {
  width: calc(--value(number) / 16 * 1rem);
}

@utility max-w-px-* {
  max-width: calc(--value(number) / 16 * 1rem);
}

@utility min-w-px-* {
  min-width: calc(--value(number) / 16 * 1rem);
}

/* Height */
@utility h-px-* {
  height: calc(--value(number) / 16 * 1rem);
}

@utility max-h-px-* {
  max-height: calc(--value(number) / 16 * 1rem);
}

@utility min-h-px-* {
  min-height: calc(--value(number) / 16 * 1rem);
}
```

**Ejemplos:**
```html
<!-- Cuadrado de 64x64px -->
<div class="size-px-64 bg-primary"></div>

<!-- Ancho específico -->
<div class="w-px-320 bg-surface p-4">
  320px de ancho (20rem)
</div>

<!-- Contenedor con límites -->
<div class="min-w-px-200 max-w-px-800 bg-secondary">
  Ancho entre 200px y 800px
</div>

<!-- Altura fija -->
<div class="h-px-500 bg-tertiary">
  500px de alto
</div>
```

### 2. Padding

```css
/* Padding en todos los lados */
@utility p-px-* {
  padding: calc(--value(number) / 16 * 1rem);
}

/* Padding horizontal (izquierda + derecha) */
@utility px-px-* {
  padding-left: calc(--value(number) / 16 * 1rem);
  padding-right: calc(--value(number) / 16 * 1rem);
}

/* Padding vertical (arriba + abajo) */
@utility py-px-* {
  padding-top: calc(--value(number) / 16 * 1rem);
  padding-bottom: calc(--value(number) / 16 * 1rem);
}

/* Padding individual */
@utility pt-px-* { padding-top: calc(--value(number) / 16 * 1rem); }
@utility pr-px-* { padding-right: calc(--value(number) / 16 * 1rem); }
@utility pb-px-* { padding-bottom: calc(--value(number) / 16 * 1rem); }
@utility pl-px-* { padding-left: calc(--value(number) / 16 * 1rem); }
```

**Ejemplos:**
```html
<!-- Padding uniforme -->
<div class="p-px-32 bg-primary">
  32px de padding en todos los lados
</div>

<!-- Padding horizontal y vertical diferentes -->
<div class="px-px-24 py-px-16 bg-secondary">
  24px horizontal, 16px vertical
</div>

<!-- Padding específico -->
<div class="pt-px-20 pb-px-40 px-px-16 bg-tertiary">
  Top: 20px, Bottom: 40px, Lateral: 16px
</div>
```

### 3. Margin (incluye negativos)

```css
/* Margin en todos los lados */
@utility m-px-* {
  margin: calc(--value(number) / 16 * 1rem);
}

/* Margin negativo */
@utility -m-px-* {
  margin: calc(--value(number) / -16 * 1rem);
}

/* Margin horizontal */
@utility mx-px-* {
  margin-left: calc(--value(number) / 16 * 1rem);
  margin-right: calc(--value(number) / 16 * 1rem);
}

@utility -mx-px-* {
  margin-left: calc(--value(number) / -16 * 1rem);
  margin-right: calc(--value(number) / -16 * 1rem);
}

/* ... y todas las variaciones */
```

**Ejemplos:**
```html
<!-- Margin normal -->
<div class="m-px-20 bg-primary">
  20px de margin
</div>

<!-- Margin negativo (overlap) -->
<div class="relative">
  <div class="h-px-200 bg-secondary"></div>
  <div class="-mt-px-50 bg-tertiary">
    Sube 50px sobre el elemento anterior
  </div>
</div>

<!-- Centrado con margin -->
<div class="w-px-600 mx-auto">
  Centrado horizontalmente
</div>
```

### 4. Gaps (para Flex y Grid)

```css
@utility gap-px-* {
  gap: calc(--value(number) / 16 * 1rem);
}

@utility gap-x-px-* {
  column-gap: calc(--value(number) / 16 * 1rem);
}

@utility gap-y-px-* {
  row-gap: calc(--value(number) / 16 * 1rem);
}
```

**Ejemplos:**
```html
<!-- Gap uniforme -->
<div class="flex gap-px-20">
  <div>Item 1</div>
  <div>Item 2</div>
</div>

<!-- Gap diferente por eje -->
<div class="flex flex-wrap gap-x-px-16 gap-y-px-24">
  <div>Card 1</div>
  <div>Card 2</div>
  <div>Card 3</div>
</div>
```

### 5. Posicionamiento

```css
/* Posiciones absolutas */
@utility top-px-* {
  top: calc(--value(number) / 16 * 1rem);
}

@utility -top-px-* {
  top: calc(--value(number) / -16 * 1rem);
}

/* ... similar para bottom, left, right, inset */
```

**Ejemplos:**
```html
<div class="relative h-96">
  <!-- Posicionado a 20px del top -->
  <div class="absolute top-px-20 left-px-30">
    Posición exacta
  </div>

  <!-- Posición negativa -->
  <div class="absolute -top-px-10 right-0">
    Sale del contenedor 10px hacia arriba
  </div>

  <!-- Inset uniforme -->
  <div class="absolute inset-px-15 bg-primary/20">
    15px de separación en todos los lados
  </div>
</div>
```

### ¿Cuándo usar px-* vs la escala de Tailwind?

**Usa la escala de Tailwind (`p-4`, `w-64`) cuando:**
- ✅ Necesitas consistencia con el sistema de diseño
- ✅ Los valores son múltiplos de 4px (1rem = 16px)
- ✅ Quieres aprovechar las utilidades responsive

**Usa las utilidades px-* cuando:**
- ✅ Necesitas valores exactos específicos del diseño (ej: 320px)
- ✅ Trabajas con medidas de un diseño en Figma/Photoshop
- ✅ El valor no encaja en la escala de Tailwind

---

## 🔘 Sistema de Botones con Pseudo-elementos

### Anatomía de un Botón

Los botones en este core usan una técnica avanzada con **pseudo-elementos** para crear efectos hover sofisticados.

### 1. Reset de Botones

```css
@layer components {
  /* Reseteo básico para todos los botones y enlaces */
  :where(a, button) {
    @apply !no-underline      /* Sin subrayado */
           text-button        /* Tamaño tipográfico */
           !font-semibold     /* Semi-negritas */
           text-outline;      /* Color de texto por defecto */

    & span {
      @apply z-10;  /* ⬅️ IMPORTANTE: pone el texto encima */
    }
  }

  /* Estilos base compartidos */
  :is(.btn-primary, .btn-secondary, .btn-tertiary) {
    @apply inline-flex      /* Flex para alinear contenido */
           flex-center      /* Centra el contenido */
           border-3         /* Borde de 3px */
           px-6 py-2        /* Padding interno */
           rounded-full     /* Bordes redondeados */
           transition-all   /* Transiciones suaves */
           duration-300     /* Duración 300ms */
           max-w-px-400;    /* Máximo 400px */
  }
}
```

**¿Por qué `& span { z-10 }`?**

El botón tiene un pseudo-elemento `::before` que crece en hover. El `z-10` asegura que el texto del `<span>` quede **encima** del pseudo-elemento.

### 2. Botón Primary (con efecto hover)

```css
@utility btn-primary {
  @apply border-primary     /* Borde color primario */
         bg-primary         /* Fondo color primario */
         text-on-primary    /* Texto sobre primario */
         relative;          /* Necesario para ::before */

  /* Pseudo-elemento para el efecto hover */
  &:before {
    @apply block              /* Es un elemento de bloque */
           content-['']       /* Sin contenido, solo visual */
           size-px            /* Tamaño inicial mínimo */
           position-center    /* Centrado absoluto */
           rounded-full       /* Redondo */
           origin-center      /* Crece desde el centro */
           duration-300       /* Duración de animación */
           transition-all;    /* Transición suave */
  }

  /* Estados hover, active, focus */
  &:hover, &:active, &:focus {
    @apply text-on-primary-dk;  /* Cambia color de texto */

    &::before {
      @apply aspect-square       /* Mantiene proporción */
             bg-primary-dk;      /* Fondo oscuro */

      /* Crece 10px más que el botón */
      width: calc(100% + 10px);
      height: calc(100% + 10px);
    }
  }
}
```

### ¿Cómo funciona `position-center`?

Recordemos que `position-center` es:
```css
position: absolute;
top: 50%;
left: 50%;
transform: translate(-50%, -50%);
```

Esto centra el pseudo-elemento. Al crecer desde `origin-center`, se expande uniformemente desde el centro.

### 3. Adaptación Automática en Fondos Oscuros

```css
@layer utilities {
  :is(.bg-primary-dk, .bg-primary, .bg-error) {
    & .btn-primary {
      @apply border-on-primary   /* Borde blanco */
             overflow-hidden;     /* Esconde el desbordamiento */
    }
  }
}
```

**¿Qué hace esto?**

Cuando el botón está dentro de un fondo oscuro, automáticamente cambia su borde a blanco para mantener contraste.

**Ejemplo:**
```html
<!-- Botón en fondo claro -->
<section class="bg-background">
  <button class="btn-primary">
    <span>Acción</span>
    <!-- Borde naranja -->
  </button>
</section>

<!-- Botón en fondo oscuro -->
<section class="bg-primary-dk">
  <button class="btn-primary">
    <span>Acción</span>
    <!-- Borde blanco automáticamente -->
  </button>
</section>
```

### Uso Correcto del Botón

```html
<!-- ✅ CORRECTO: con span -->
<button class="btn-primary">
  <span>Texto del botón</span>
</button>

<!-- ✅ CORRECTO: con span e icono -->
<a href="#" class="btn-primary">
  <span>Descargar</span>
  <svg>...</svg>
</a>

<!-- ❌ INCORRECTO: sin span -->
<button class="btn-primary">
  Texto sin span
  <!-- No tendrá z-10, puede quedar detrás del ::before -->
</button>
```

### Ejemplo Completo con Estados

```html
<div class="flex-container-px-16">
  <!-- Estado normal -->
  <button class="btn-primary">
    <span>Normal</span>
  </button>

  <!-- Estado hover (simular con :hover en CSS) -->
  <button class="btn-primary hover">
    <span>Hover</span>
  </button>

  <!-- En fondo oscuro -->
  <div class="bg-primary p-6">
    <button class="btn-primary">
      <span>En fondo oscuro</span>
    </button>
  </div>
</div>
```

---

## 🖼️ Utilidad para Imágenes de Fondo

### La Clase bg-cover

Esta utilidad personalizada está diseñada para trabajar con imágenes de fondo en elementos HTML, configurando todas las propiedades necesarias para una correcta visualización.

### Implementación

```css
@utility bg-cover {
  background-position: center center;  /* Centra la imagen */
  background-repeat: no-repeat;        /* No repite la imagen */
  background-size: 100%;               /* Cubre el 100% del espacio */
  display: block;                      /* Elemento de bloque */
  font-size: 0;                        /* Oculta texto fallback */
  overflow: hidden;                    /* Esconde desbordamientos */
  text-indent: -999999px;              /* Mueve texto fuera de vista */
}
```

### ¿Cómo Funciona?

**Propiedades de background:**
- `background-position: center center` → Centra la imagen horizontal y verticalmente
- `background-repeat: no-repeat` → Evita que la imagen se repita
- `background-size: 100%` → Hace que la imagen cubra todo el espacio disponible

**Propiedades para ocultar texto:**
- `font-size: 0` → Reduce el tamaño del texto a 0
- `text-indent: -999999px` → Mueve el texto fuera de la vista
- `overflow: hidden` → Esconde cualquier contenido que se desborde

Estas técnicas permiten mantener el texto en el HTML (para SEO y accesibilidad) pero ocultarlo visualmente cuando hay una imagen de fondo.

### Uso con Tailwind

Esta clase se usa **junto con** la utilidad de Tailwind `bg-[url('...')]`:

```html
<!-- ✅ CORRECTO: Combinar ambas clases -->
<div class="bg-[url('../images/hero.jpg')] bg-cover h-px-500">
  Texto alternativo para SEO
</div>

<!-- Con posicionamiento relativo para contenido -->
<div class="bg-[url('../images/banner.jpg')] bg-cover h-px-400 relative">
  <div class="position-center text-h1 text-white">
    Contenido sobre la imagen
  </div>
</div>
```

### ⚠️ Limitación Importante

**La sintaxis `bg-[url('...')]` SOLO funciona cuando se escribe directamente en el HTML.**

```html
<!-- ✅ FUNCIONA: URL directa en la clase -->
<div class="bg-[url('../images/foto.jpg')] bg-cover"></div>

<!-- ❌ NO FUNCIONA: URL generada dinámicamente -->
<div class="bg-[url('<?= $imagen ?>')] bg-cover"></div>

<!-- ❌ NO FUNCIONA: Con JavaScript -->
<div class="bg-[url(${imagenUrl})] bg-cover"></div>

<!-- ❌ NO FUNCIONA: Con Twig -->
<div class="bg-[url('{{ imagen }}')] bg-cover"></div>
```

### Solución para URLs Dinámicas

Si necesitas URLs dinámicas, usa **estilos inline**:

```html
<!-- ✅ SOLUCIÓN: Style inline con bg-cover -->
<div
  class="bg-cover h-px-500"
  style="background-image: url('../images/<?= $imagen ?>');"
>
  Texto alternativo
</div>

<!-- Con JavaScript -->
<div
  id="hero"
  class="bg-cover h-px-400"
>
  Texto alternativo
</div>

<script>
  const hero = document.getElementById('hero');
  hero.style.backgroundImage = `url('${imagenUrl}')`;
</script>

<!-- Con Twig -->
<div
  class="bg-cover h-px-500"
  style="background-image: url('{{ imagen }}');"
>
  Texto alternativo
</div>
```

### Ejemplos Prácticos

**Hero section con imagen de fondo:**
```html
<section class="bg-[url('../images/hero-bg.jpg')] bg-cover h-screen relative">
  Hero Section

  <!-- Overlay oscuro -->
  <div class="position-full bg-primary-dk/60"></div>

  <!-- Contenido -->
  <div class="position-center z-10 text-center">
    <h1 class="text-h1 text-white mb-4">Bienvenido</h1>
    <p class="text-header text-white mb-8">
      Subtítulo descriptivo
    </p>
    <button class="btn-primary">
      <span>Comenzar</span>
    </button>
  </div>
</section>
```

**Card con imagen de fondo:**
```html
<article class="relative rounded-lg overflow-hidden h-px-400">
  <!-- Imagen de fondo -->
  <div class="bg-[url('../images/card-bg.jpg')] bg-cover position-full">
    Card con imagen de fondo
  </div>

  <!-- Contenido sobre la imagen -->
  <div class="position-full bg-gradient-to-t from-primary-dk/90 to-transparent">
    <div class="position-b p-6">
      <h3 class="text-h3 text-white">Título de la Card</h3>
      <p class="text-base text-white/90">Descripción</p>
    </div>
  </div>
</article>
```

**Banner con contenido centrado:**
```html
<div class="bg-[url('../images/banner.jpg')] bg-cover h-px-300 flex-center relative">
  Banner Section

  <!-- Overlay -->
  <div class="position-full bg-primary/50"></div>

  <!-- Texto -->
  <div class="relative z-10 text-center">
    <h2 class="text-h2 text-white">Título del Banner</h2>
  </div>
</div>
```

**Galería con imágenes dinámicas (PHP):**
```php
<?php
$imagenes = ['foto1.jpg', 'foto2.jpg', 'foto3.jpg'];
?>

<div class="flex-grid-3 gap-y-4">
  <?php foreach($imagenes as $img): ?>
    <div
      class="bg-cover h-px-300 rounded-lg"
      style="background-image: url('../images/<?= $img ?>');"
    >
      <?= $img ?>
    </div>
  <?php endforeach; ?>
</div>
```

### Combinaciones Útiles

```html
<!-- Con altura y redondeo -->
<div class="bg-[url('../img/photo.jpg')] bg-cover h-px-400 rounded-lg">
  Foto
</div>

<!-- Con ancho y altura fijos -->
<div class="bg-[url('../img/avatar.jpg')] bg-cover size-px-200 rounded-full">
  Avatar
</div>

<!-- Responsive: altura diferente por breakpoint -->
<div class="bg-[url('../img/hero.jpg')] bg-cover
            h-px-300 md:h-px-500 lg:h-screen">
  Hero responsive
</div>

<!-- Con aspect ratio -->
<div class="bg-[url('../img/banner.jpg')] bg-cover aspect-video">
  Banner 16:9
</div>
```

### Diferencia con bg-cover de Tailwind

Tailwind tiene su propia utilidad `bg-cover`, pero esta es diferente:

```html
<!-- bg-cover de Tailwind: solo background-size: cover -->
<div class="bg-[url('...')] bg-cover bg-center bg-no-repeat">
  Necesitas múltiples clases
</div>

<!-- bg-cover del Core: todo incluido -->
<div class="bg-[url('...')] bg-cover">
  Una sola clase adicional
</div>
```

**Nuestra `bg-cover` personalizada incluye:**
- ✅ Posicionamiento centrado
- ✅ Sin repetición
- ✅ Tamaño al 100%
- ✅ Ocultación de texto para SEO
- ✅ Todo en una sola clase

---

## 🧪 Herramientas de Debugging

### 1. Test (Visualiza Jerarquía)

```css
@utility test {
  @apply !border-3 !border-red-500;  /* Padre: rojo */

  & > * {
    @apply !border-3 !border-yellow-400;  /* Hijos: amarillo */

    & > * {
      @apply !border-3 !border-green-400;  /* Nietos: verde */

      & > * {
        @apply !border-3 !border-blue-400;  /* Bisnietos: azul */

        & > * {
          @apply !border-3 !border-purple-400;  /* Tataranietos: púrpura */

          & > * {
            @apply !border-3 !border-yellow-700;  /* Más: amarillo oscuro */
          }
        }
      }
    }
  }
}
```

**¿Para qué sirve?**

Visualiza la estructura del DOM con colores. Útil para detectar:
- Niveles de anidamiento excesivos
- Elementos inesperados
- Problemas de layout

**Uso:**
```html
<section class="test">
  <div>  <!-- Rojo -->
    <article>  <!-- Amarillo -->
      <h2>Título</h2>  <!-- Verde -->
      <p>  <!-- Azul -->
        <span>Texto</span>  <!-- Púrpura -->
      </p>
    </article>
  </div>
</section>
```

### 2. Test Responsive (Muestra Breakpoint Actual)

```css
@utility test-responsive {
  &, &.test, &.test-son {
    @apply !border-3
           !border-gray-200          /* < 350px: gris */
           xs:!border-red-500        /* 350px+: rojo */
           sm:!border-yellow-400     /* 576px+: amarillo */
           md:!border-green-400      /* 768px+: verde */
           lg:!border-blue-400       /* 992px+: azul */
           xl:!border-purple-400     /* 1200px+: púrpura */
           2xl:!border-yellow-700    /* 1400px+: amarillo oscuro */
           !relative

           /* Pseudo-elemento con el badge */
           before:!absolute
           before:!top-0
           before:!right-0
           before:!font-semibold
           before:!px-2
           before:!pb-0.5
           before:!rounded-bl-lg
           before:!text-small
           before:!text-black
           before:!block

           /* Contenido del badge por breakpoint */
           before:!content-['📱<_350']
           before:!bg-gray-200

           xs:before:!content-['📱xs:_350']
           xs:before:!bg-red-500
           xs:before:!text-white

           sm:before:!content-['📱sm:_576']
           sm:before:!bg-yellow-400
           sm:before:!text-black

           md:before:!content-['💻md:_768']
           md:before:!bg-green-400

           lg:before:!content-['💻lg:_992']
           lg:before:!bg-blue-400

           xl:before:!content-['🖥️xl:_1200']
           xl:before:!bg-purple-400

           2xl:before:!content-['🖥️2xl:_1400']
           2xl:before:!bg-yellow-700
           2xl:before:!text-white;
  }
}
```

**¿Qué hace esto?**

Crea un **badge visual** en la esquina superior derecha que muestra:
- El breakpoint actual
- El ancho mínimo de ese breakpoint
- Un emoji representativo (📱 para móvil, 💻 para tablet, 🖥️ para desktop)

**Cómo funciona el `before:!content-[...]`:**

```css
/* En < 350px */
before::before {
  content: '📱< 350';
  background: #f3f4f6; /* gris */
}

/* En 576px+ */
before::before {
  content: '📱sm: 576';
  background: #fbbf24; /* amarillo */
}
```

**Uso:**
```html
<section class="test-responsive">
  <!-- Verás un badge que dice el breakpoint actual -->
  <div class="container">
    <h2>Contenido</h2>
  </div>
</section>
```

### 3. Test Son (Solo Hijos Directos)

```css
@utility test-son {
  @apply !border-3 !border-red-500;  /* Padre: rojo */

  & > * {
    @apply !border-3 !border-yellow-400;  /* Hijos: amarillo */
    /* No aplica a nietos */
  }
}
```

**¿Para qué sirve?**

Visualiza solo los hijos directos, útil para debuggear:
- Sistemas de flex/grid
- Problemas de spacing
- Elementos que no deberían estar ahí

**Uso:**
```html
<div class="test-son flex-grid-3">
  <div>Hijo 1 - amarillo</div>
  <div>Hijo 2 - amarillo</div>
  <div>
    Hijo 3 - amarillo
    <p>Nieto - sin borde</p>
  </div>
</div>
```

### Cuándo Usar Cada Test

| Herramienta | Úsala para... |
|-------------|---------------|
| `test` | Ver toda la estructura del DOM |
| `test-responsive` | Verificar breakpoints y diseño responsive |
| `test-son` | Debuggear layouts flex/grid |

**Recuerda:** Estas clases usan `!important` para sobrescribir cualquier estilo. **Quítalas antes de producción.**

---

## 🔤 Fuentes Personalizadas

### Sistema de Fuentes

El core define tres familias tipográficas con múltiples pesos cada una.

### 1. Definición de Variables

```css
@theme {
  --font-serif: 'Merriweather', serif;   /* Títulos elegantes */
  --font-sans: 'OpenSans', sans-serif;   /* Texto legible */
  --font-mono: 'RobotoMono', monospace;  /* Código */
}
```

**¿Qué hace @theme aquí?**

Define variables CSS que Tailwind convierte automáticamente en clases:
- `font-serif` → usa Merriweather
- `font-sans` → usa OpenSans
- `font-mono` → usa RobotoMono

### 2. Importación de Fuentes (@font-face)

```css
@layer base {
  @font-face {
    font-family: 'Merriweather';
    src: url('../fonts/Merriweather24pt-Bold.woff2') format('woff2'),
         url('../fonts/Merriweather24pt-Bold.woff') format('woff');
    font-weight: bold;
    font-style: normal;
    font-display: swap;
  }
}
```

**Desglose:**

- `font-family`: Nombre que usarás en CSS
- `src`: Rutas a los archivos de fuente (woff2 primero para mejor soporte)
- `font-weight`: Peso específico de esta variante
- `font-style`: Normal o italic
- `font-display: swap`: Muestra fuente del sistema mientras carga la custom

### 3. Múltiples Pesos

**OpenSans (sans) - 4 pesos:**
```css
/* Bold (700) */
@font-face {
  font-family: 'OpenSans';
  src: url('../fonts/OpenSans-Bold.woff2') format('woff2');
  font-weight: bold;  /* 700 */
}

/* SemiBold (600) */
@font-face {
  font-family: 'OpenSans';
  src: url('../fonts/OpenSans-SemiBold.woff2') format('woff2');
  font-weight: 600;
}

/* Medium (500) */
@font-face {
  font-family: 'OpenSans';
  src: url('../fonts/OpenSans-Medium.woff2') format('woff2');
  font-weight: 500;
}

/* Regular (400) */
@font-face {
  font-family: 'OpenSans';
  src: url('../fonts/OpenSans-Regular.woff2') format('woff2');
  font-weight: normal;  /* 400 */
}
```

**¿Por qué mismo `font-family` pero diferente `font-weight`?**

Esto permite que el navegador elija automáticamente la variante correcta:

```html
<p class="font-sans font-normal">Regular (400)</p>
<p class="font-sans font-medium">Medium (500)</p>
<p class="font-sans font-semibold">SemiBold (600)</p>
<p class="font-sans font-bold">Bold (700)</p>
```

### 4. Formatos de Fuente

```css
src: url('../fonts/OpenSans-Bold.woff2') format('woff2'),
     url('../fonts/OpenSans-Bold.woff') format('woff');
```

**¿Por qué dos formatos?**

- **WOFF2**: Más moderno, mejor compresión, el navegador lo intenta primero
- **WOFF**: Fallback para navegadores más antiguos

**Orden de carga:**
1. Navegador intenta WOFF2
2. Si no es compatible, usa WOFF
3. Si ninguno funciona, usa fuente del sistema

### Uso en el Proyecto

```html
<!-- Título con serif -->
<h1 class="font-serif text-h1 font-bold">
  Merriweather Bold
</h1>

<!-- Párrafo con sans -->
<p class="font-sans text-base font-normal">
  OpenSans Regular para lectura cómoda
</p>

<!-- Código con mono -->
<code class="font-mono text-small">
  const x = 42;
</code>
```

### Aplicación Automática

Recuerda que las clases tipográficas ya tienen las fuentes asignadas:

```css
@layer base {
  /* Títulos usan serif automáticamente */
  :is(.text-h1, .text-h2, .text-h3, .text-h4) {
    @apply font-serif font-bold;
  }

  /* Texto normal usa sans automáticamente */
  :is(.text-base, .text-small) {
    @apply font-sans;
  }
}
```

Entonces:
```html
<!-- Ya tiene font-serif aplicado -->
<h2 class="text-h2">Título automático</h2>

<!-- Ya tiene font-sans aplicado -->
<p class="text-base">Párrafo automático</p>
```

---

## 🎯 Casos de Uso Completos

### Ejemplo 1: Card de Producto

```html
<article class="bg-surface rounded-lg overflow-hidden">
  <!-- Imagen con badge -->
  <div class="relative h-px-300">
    <img src="producto.jpg" alt="Producto" class="position-full object-cover">
    <span class="position-tl bg-error px-3 py-1 text-small">
      -20%
    </span>
  </div>

  <!-- Contenido -->
  <div class="p-px-24">
    <h3 class="text-h4 text-primary mb-2">Nombre del Producto</h3>
    <p class="text-base mb-4">
      Descripción breve del producto que se adapta automáticamente.
    </p>

    <!-- Precio -->
    <div class="flex items-center gap-2 mb-4">
      <span class="text-header text-secondary font-bold">$99.99</span>
      <span class="text-small line-through text-on-surface/50">$124.99</span>
    </div>

    <!-- Botón -->
    <button class="btn-primary w-full">
      <span>Agregar al carrito</span>
    </button>
  </div>
</article>
```

**¿Qué técnicas usa?**
- ✅ `bg-surface` con `text-on-surface` automático
- ✅ `position-full` para imagen que cubre todo
- ✅ `position-tl` para badge en esquina
- ✅ Tamaños en píxeles (`h-px-300`, `p-px-24`)
- ✅ Colores semánticos (`text-primary`, `bg-error`)
- ✅ Tipografía responsive (`text-h4`, `text-base`)

---

### Ejemplo 2: Hero Section Responsive

```html
<section class="bg-primary py-16">
  <div class="container">
    <div class="flex-grid-2 lg:flex-grid-2 gap-y-8 items-center">
      <!-- Contenido texto -->
      <div class="flex-container-px-24">
        <h1 class="text-h1">
          Transforma tu Negocio con Nuestro Core CSS
        </h1>
        <p class="text-header">
          Sistema de diseño profesional que acelera tu desarrollo
          y garantiza consistencia visual.
        </p>
        <div class="flex gap-4 flex-wrap">
          <button class="btn-primary">
            <span>Comenzar ahora</span>
          </button>
          <button class="btn-primary">
            <span>Ver demo</span>
          </button>
        </div>
      </div>

      <!-- Imagen -->
      <div class="relative h-px-500">
        <img
          src="hero.jpg"
          alt="Hero"
          class="position-full object-cover rounded-lg"
        >

        <!-- Badge flotante -->
        <div class="position-br -mb-px-30 -mr-px-30 bg-secondary px-6 py-4 rounded-lg">
          <p class="text-h3 font-bold">+500</p>
          <p class="text-small">Proyectos</p>
        </div>
      </div>
    </div>
  </div>
</section>
```

**¿Qué técnicas usa?**
- ✅ `bg-primary` aplica `text-on-primary` automáticamente
- ✅ `flex-grid-2` con responsive `lg:flex-grid-2`
- ✅ `flex-container-px-24` organiza contenido con gap
- ✅ `position-br` con márgenes negativos para efecto flotante
- ✅ Container responsive que se adapta al viewport

---

### Ejemplo 3: Dashboard Layout

```html
<div class="min-h-screen bg-background">
  <!-- Header -->
  <header class="bg-primary">
    <div class="container-full">
      <div class="flex justify-between items-center h-px-80 px-px-24">
        <h1 class="text-header">Dashboard</h1>
        <button class="btn-primary">
          <span>Cerrar sesión</span>
        </button>
      </div>
    </div>
  </header>

  <!-- Main Content -->
  <main class="container py-16">
    <!-- Stats Grid -->
    <div class="flex-grid-2 md:flex-grid-4 gap-y-6 mb-12">
      <!-- Stat Card 1 -->
      <div class="bg-surface p-6 rounded-lg relative">
        <div class="position-tr">
          <span class="text-h2">📊</span>
        </div>
        <p class="text-small text-secondary mb-2">Total Ventas</p>
        <p class="text-h3 text-primary">$45,231</p>
        <p class="text-small text-success mt-2">↑ 12.5%</p>
      </div>

      <!-- Stat Card 2 -->
      <div class="bg-surface p-6 rounded-lg relative">
        <div class="position-tr">
          <span class="text-h2">👥</span>
        </div>
        <p class="text-small text-secondary mb-2">Usuarios</p>
        <p class="text-h3 text-primary">1,234</p>
        <p class="text-small text-success mt-2">↑ 8.2%</p>
      </div>

      <!-- Stat Card 3 -->
      <div class="bg-surface p-6 rounded-lg relative">
        <div class="position-tr">
          <span class="text-h2">📦</span>
        </div>
        <p class="text-small text-secondary mb-2">Productos</p>
        <p class="text-h3 text-primary">567</p>
        <p class="text-small text-info mt-2">→ 0%</p>
      </div>

      <!-- Stat Card 4 -->
      <div class="bg-surface p-6 rounded-lg relative">
        <div class="position-tr">
          <span class="text-h2">⭐</span>
        </div>
        <p class="text-small text-secondary mb-2">Rating</p>
        <p class="text-h3 text-primary">4.8</p>
        <p class="text-small text-success mt-2">↑ 0.3</p>
      </div>
    </div>

    <!-- Chart Section -->
    <div class="bg-surface p-px-32 rounded-lg">
      <h2 class="text-h3 text-primary mb-6">Ventas del Mes</h2>
      <div class="h-px-400 bg-background rounded flex-center">
        <p class="text-base">Gráfico aquí</p>
      </div>
    </div>
  </main>
</div>
```

**¿Qué técnicas usa?**
- ✅ Layout completo con header fijo y contenido
- ✅ Grid responsive de stats (2 columnas móvil, 4 desktop)
- ✅ `position-tr` para iconos en esquina
- ✅ Colores semánticos para estados (success, info, error)
- ✅ Mezcla de utilidades de tamaño (`h-px-80`, `p-6`)

---

### Ejemplo 4: Modal Centrado

```html
<!-- Overlay -->
<div class="position-full bg-primary-dk/80 flex-center z-50">
  <!-- Modal -->
  <div class="bg-surface rounded-lg max-w-px-500 w-[90%] relative">
    <!-- Botón cerrar -->
    <button class="position-tr p-4 text-h4 hover:text-error transition-colors">
      ✕
    </button>

    <!-- Header -->
    <div class="p-px-32 pb-px-16">
      <h2 class="text-h3 text-primary">Confirmar Acción</h2>
    </div>

    <!-- Content -->
    <div class="px-px-32 pb-px-32">
      <p class="text-base mb-6">
        ¿Estás seguro de que deseas realizar esta acción?
        Esta operación no se puede deshacer.
      </p>

      <!-- Actions -->
      <div class="flex gap-4 justify-end">
        <button class="btn-primary bg-surface border-outline text-on-surface hover:bg-outline">
          <span>Cancelar</span>
        </button>
        <button class="btn-primary">
          <span>Confirmar</span>
        </button>
      </div>
    </div>
  </div>
</div>
```

**¿Qué técnicas usa?**
- ✅ `position-full` para overlay que cubre toda la pantalla
- ✅ `flex-center` para centrar el modal
- ✅ `position-tr` para botón cerrar en esquina
- ✅ `max-w-px-500` para ancho máximo del modal
- ✅ `bg-primary-dk/80` para overlay semi-transparente

---

### Ejemplo 5: Formulario Completo

```html
<section class="bg-background py-16">
  <div class="container-md">
    <div class="bg-surface rounded-lg p-px-32">
      <!-- Header -->
      <header class="mb-8">
        <h2 class="text-h2 text-primary">Contáctanos</h2>
        <p class="text-base text-secondary">
          Completa el formulario y te responderemos pronto
        </p>
      </header>

      <!-- Form -->
      <form class="flex-container-px-20">
        <!-- Nombre -->
        <div class="flex-container-px-8">
          <label class="text-button font-semibold" for="nombre">
            Nombre completo
          </label>
          <input
            type="text"
            id="nombre"
            class="border-3 border-outline rounded-lg px-4 py-3 text-base
                   focus:border-primary focus:outline-none transition-colors"
            placeholder="Juan Pérez"
          >
        </div>

        <!-- Email -->
        <div class="flex-container-px-8">
          <label class="text-button font-semibold" for="email">
            Email
          </label>
          <input
            type="email"
            id="email"
            class="border-3 border-outline rounded-lg px-4 py-3 text-base
                   focus:border-primary focus:outline-none transition-colors"
            placeholder="juan@example.com"
          >
        </div>

        <!-- Mensaje -->
        <div class="flex-container-px-8">
          <label class="text-button font-semibold" for="mensaje">
            Mensaje
          </label>
          <textarea
            id="mensaje"
            rows="5"
            class="border-3 border-outline rounded-lg px-4 py-3 text-base
                   focus:border-primary focus:outline-none transition-colors
                   resize-none"
            placeholder="Tu mensaje aquí..."
          ></textarea>
        </div>

        <!-- Checkbox -->
        <div class="flex items-start gap-3">
          <input
            type="checkbox"
            id="terminos"
            class="mt-1 size-px-20 accent-primary cursor-pointer"
          >
          <label for="terminos" class="text-small cursor-pointer">
            Acepto los términos y condiciones y la política de privacidad
          </label>
        </div>

        <!-- Submit -->
        <button type="submit" class="btn-primary w-full">
          <span>Enviar mensaje</span>
        </button>

        <!-- Mensaje de éxito (oculto por defecto) -->
        <div class="bg-success p-4 rounded-lg hidden">
          <p class="text-base font-semibold">
            ✓ Mensaje enviado exitosamente
          </p>
        </div>
      </form>
    </div>
  </div>
</section>
```

**¿Qué técnicas usa?**
- ✅ `container-md` para formulario más estrecho
- ✅ `flex-container-px-20` organiza campos con gap uniforme
- ✅ `flex-container-px-8` para label + input con gap pequeño
- ✅ `focus:border-primary` para estados interactivos
- ✅ `bg-success` para mensajes de confirmación

---

## 📚 Mejores Prácticas

### ✅ Hacer

1. **Usa colores semánticos siempre**
   ```html
   <!-- ✅ BIEN -->
   <button class="bg-primary">Acción</button>

   <!-- ❌ MAL -->
   <button class="bg-orange-500">Acción</button>
   ```

2. **Envuelve texto de botones en `<span>`**
   ```html
   <!-- ✅ BIEN -->
   <button class="btn-primary">
     <span>Texto</span>
   </button>

   <!-- ❌ MAL -->
   <button class="btn-primary">Texto</button>
   ```

3. **Aprovecha las clases tipográficas**
   ```html
   <!-- ✅ BIEN -->
   <h1 class="text-h1">Título</h1>

   <!-- ❌ MAL -->
   <h1 class="text-5xl font-bold leading-tight">Título</h1>
   ```

4. **Usa `flex-grid` en lugar de CSS Grid cuando sea posible**
   ```html
   <!-- ✅ BIEN -->
   <div class="flex-grid-3 gap-y-4">
     <div>Item</div>
   </div>

   <!-- ❌ MAL -->
   <div class="grid grid-cols-3 gap-4">
     <div>Item</div>
   </div>
   ```

5. **Utiliza containers apropiados según el contenido**
   ```html
   <!-- ✅ BIEN -->
   <!-- Hero: ancho completo -->
   <div class="container-full">...</div>

   <!-- Contenido: ancho adaptable -->
   <div class="container">...</div>

   <!-- Texto: ancho estrecho -->
   <div class="container-md">...</div>
   ```

6. **Usa herramientas de test durante desarrollo**
   ```html
   <!-- ✅ Durante desarrollo -->
   <section class="test-responsive">
     <div class="flex-grid-3 test-son">
       ...
     </div>
   </section>

   <!-- ✅ En producción (quita las clases test) -->
   <section>
     <div class="flex-grid-3">
       ...
     </div>
   </section>
   ```

### ❌ Evitar

1. **No uses colores literales para componentes reutilizables**
   ```html
   <!-- ❌ MAL -->
   <div class="bg-blue-500 text-white">
     Difícil de cambiar
   </div>
   ```

2. **No escribas media queries manuales para tipografía**
   ```css
   /* ❌ MAL */
   .mi-titulo {
     font-size: 24px;
   }
   @media (min-width: 768px) {
     .mi-titulo { font-size: 32px; }
   }

   /* ✅ BIEN */
   <h2 class="text-h3">Mi Título</h2>
   ```

3. **No mezcles sistemas de medida inconsistentemente**
   ```html
   <!-- ❌ MAL -->
   <div class="p-4 h-px-320 gap-6 w-px-400">
     Mezcla confusa
   </div>

   <!-- ✅ BIEN: Consistente con escala de Tailwind -->
   <div class="p-4 h-80 gap-6 w-96">
     ...
   </div>

   <!-- ✅ BIEN: Consistente con px-* -->
   <div class="p-px-16 h-px-320 gap-px-24 w-px-400">
     ...
   </div>
   ```

4. **No ignores el sistema de posicionamiento**
   ```html
   <!-- ❌ MAL -->
   <div class="absolute top-0 left-1/2 -translate-x-1/2">
     Texto centrado arriba
   </div>

   <!-- ✅ BIEN -->
   <div class="position-t-center">
     Texto centrado arriba
   </div>
   ```

5. **No dejes las clases de test en producción**
   ```html
   <!-- ❌ MAL en producción -->
   <section class="test-responsive">
     ...
   </section>

   <!-- ✅ BIEN -->
   <section>
     ...
   </section>
   ```

---

## 🔄 Flujo de Trabajo Recomendado

### 1. Planificación
- Define los colores del proyecto en `_colors.css`
- Ajusta breakpoints si es necesario en `_containers.css`
- Verifica que las fuentes estén cargadas correctamente

### 2. Desarrollo
- Usa `test-responsive` para verificar breakpoints
- Usa `test` o `test-son` para debuggear layouts
- Aprovecha `flex-grid` para layouts rápidos
- Utiliza colores semánticos desde el inicio

### 3. Refinamiento
- Ajusta espaciados con utilidades `px-*`
- Personaliza tipografía si es necesario con la calculadora clamp
- Optimiza componentes reutilizables

### 4. Producción
- **Quita todas las clases `test`**
- Verifica que todos los botones tengan `<span>`
- Confirma que los colores sean semánticos
- Valida el contraste de colores

---

## 📖 Glosario de Conceptos

### @theme
Directiva de Tailwind v4 para definir variables CSS que se convierten automáticamente en utilidades.

### @utility
Define una nueva clase de utilidad personalizada que no existe en Tailwind base.

### @layer
Organiza CSS en capas (base, components, utilities) para controlar especificidad.

### clamp()
Función CSS para crear valores fluidos entre un mínimo y máximo.

### Colores Semánticos
Sistema de nomenclatura basado en el **rol** del color (primary, secondary) en lugar del color literal (blue, red).

### ON Colors
Colores de texto que van "sobre" (on) un color de fondo, garantizando contraste adecuado.

### flex-basis
Propiedad CSS que define el tamaño base de un elemento flex antes de distribuir espacio restante.

### --value()
Función especial de Tailwind v4 que extrae el número de una clase (ej: `flex-grid-3` → `--value(integer) = 3`).

### Pseudo-elemento (::before, ::after)
Elemento generado por CSS que no existe en el HTML pero puede ser estilizado.

### position-center
Técnica para centrar elementos usando `position: absolute`, `top: 50%`, `left: 50%` y `transform: translate(-50%, -50%)`.

### Container
Elemento que limita el ancho del contenido y lo centra horizontalmente.

### Breakpoint
Punto de ruptura donde el diseño cambia de disposición (ej: de móvil a tablet).

---

## 🎓 Ejercicios Prácticos

### Ejercicio 1: Card Simple
Crea una card con:
- Fondo `surface`
- Título en color `primary`
- Párrafo de texto normal
- Botón primario

<details>
<summary>Ver solución</summary>

```html
<article class="bg-surface p-6 rounded-lg">
  <h3 class="text-h4 text-primary mb-4">Título</h3>
  <p class="text-base mb-6">
    Descripción de la card.
  </p>
  <button class="btn-primary">
    <span>Acción</span>
  </button>
</article>
```
</details>

### Ejercicio 2: Grid Responsive
Crea un grid que sea:
- 1 columna en móvil
- 2 columnas en tablet (md)
- 3 columnas en desktop (lg)

<details>
<summary>Ver solución</summary>

```html
<div class="flex-grid md:flex-grid-2 lg:flex-grid-3 gap-y-6">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```
</details>

### Ejercicio 3: Modal Centrado
Crea un modal centrado con:
- Overlay oscuro semi-transparente
- Modal centrado
- Botón cerrar en esquina superior derecha

<details>
<summary>Ver solución</summary>

```html
<div class="position-full bg-primary-dk/80 flex-center">
  <div class="bg-surface rounded-lg max-w-px-500 relative p-6">
    <button class="position-tr p-4">✕</button>
    <h2 class="text-h3">Modal</h2>
    <p class="text-base">Contenido</p>
  </div>
</div>
```
</details>

---

## 🆘 Solución de Problemas Comunes

### El texto del botón queda detrás del efecto hover

**Problema:**
```html
<button class="btn-primary">Texto sin span</button>
```

**Solución:**
```html
<button class="btn-primary">
  <span>Texto con span</span>
</button>
```

### Los colores no cambian cuando modifico las variables

**Problema:** No reconstruiste el CSS después de cambiar `_colors.css`.

**Solución:** Reinicia tu build process (ej: `npm run dev`).

### El container no se centra

**Problema:** El padre no tiene ancho suficiente o display inadecuado.

**Solución:**
```html
<!-- Asegúrate de que el padre permita el centrado -->
<div class="w-full">
  <div class="container">
    Contenido centrado
  </div>
</div>
```

### flex-grid no funciona correctamente

**Problema:** Faltan clases o estructura incorrecta.

**Solución:**
```html
<!-- ✅ CORRECTO -->
<div class="flex-grid-3 gap-y-4">
  <div>Item 1</div>
  <div>Item 2</div>
</div>

<!-- ❌ INCORRECTO (usa gap en vez de gap-y) -->
<div class="flex-grid-3 gap-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <!-- Los elementos se moverán a la siguiente línea -->
</div>
```

### position-center no centra el elemento

**Problema:** El padre no tiene `position: relative`.

**Solución:**
```html
<div class="relative h-64">  <!-- ⬅️ Necesario -->
  <div class="position-center">
    Centrado
  </div>
</div>
```

---

## 📞 Recursos Adicionales

### Herramientas
- 🔗 [Calculadora Clamp CSS](https://elan-sk.github.io/calculadora-clamp-css/) - Para crear tipografía responsive
- 🔗 [Tailwind CSS Docs](https://tailwindcss.com/docs) - Documentación oficial de Tailwind

### Cursos de Tailwindcss
- 🔗 [Curso de Tailwincss - midudev](https://www.youtube.com/watch?v=R5EXap3vNDA) 
- 🔗 [Curso de Tailwincss - Socratech](https://www.youtube.com/watch?v=XTEyb5Dysi4&list=PLu92OXKwcPyQFNL_gTRaEMwu39EjxuBFY) 

### Contacto
Si tienes dudas o sugerencias sobre el core:
1. Revisa esta documentación
2. Consulta los archivos fuente
3. Pregunta al equipo de frontend

---

## 🎉 Conclusión

Este Core CSS te proporciona:

✅ **Sistema de colores semánticos** - Reutilización sin esfuerzo
✅ **Tipografía responsive** - Se adapta automáticamente
✅ **Utilidades de layout** - Maquetado rápido y consistente
✅ **Componentes preconfigurados** - Menos código personalizado
✅ **Herramientas de debugging** - Desarrollo más eficiente

### Próximos Pasos

1. Explora los ejemplos de código
2. Prueba las utilidades en un proyecto pequeño
3. Familiarízate con las clases de test
4. Personaliza los colores según tu proyecto
5. Comparte feedback con el equipo

**¡Feliz desarrollo! 🚀**

---

**Versión:** 2.0
**Última actualización:** Octubre 2025
**Autor:** [ELAN-SK](https://github.com/elan-sk)
**Licencia:** Uso interno
