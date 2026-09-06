---
title: "CSS Variables"
description: "Nuxt UI uses CSS variables as design tokens for flexible, consistent theming with built-in light and dark mode support."
canonical_url: "https://ui.nuxt.com/docs/getting-started/theme/css-variables"
---
# CSS Variables

> Nuxt UI uses CSS variables as design tokens for flexible, consistent theming with built-in light and dark mode support.

## Colors

Nuxt UI provides Tailwind CSS utility classes for each [semantic color](https://ui.nuxt.com/docs/getting-started/theme/design-system#semantic-colors) you define, so you can use class names like `text-error` or `bg-success`:

```vue
<template>
  <span class="text-primary">Primary</span>
  <span class="text-secondary">Secondary</span>
  <span class="text-success">Success</span>
  <span class="text-info">Info</span>
  <span class="text-warning">Warning</span>
  <span class="text-error">Error</span>
</template>
```

Each utility class uses a CSS variable to set its color for light and dark modes:

```css [Light]
:root {
  --ui-primary: var(--ui-color-primary-500);
  --ui-secondary: var(--ui-color-secondary-500);
  --ui-success: var(--ui-color-success-500);
  --ui-info: var(--ui-color-info-500);
  --ui-warning: var(--ui-color-warning-500);
  --ui-error: var(--ui-color-error-500);
}
```

```css [Dark]
.dark {
  --ui-primary: var(--ui-color-primary-400);
  --ui-secondary: var(--ui-color-secondary-400);
  --ui-success: var(--ui-color-success-400);
  --ui-info: var(--ui-color-info-400);
  --ui-warning: var(--ui-color-warning-400);
  --ui-error: var(--ui-color-error-400);
}
```

> [!TIP]
> 
> You can adjust which shade each utility class uses for light and dark mode in your `main.css` file:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-primary: var(--ui-color-primary-700);
> }
> 
> .dark {
>   --ui-primary: var(--ui-color-primary-200);
> }
> ```

> [!WARNING]
> 
> You can't use `primary: 'black'` in your [**config**](https://ui.nuxt.com/docs/getting-started/theme/design-system#runtime-configuration) because `black` doesn't have multiple shades. To use solid black or white as your primary color, set it directly in your `main.css` file:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-primary: black;
> }
> 
> .dark {
>   --ui-primary: white;
> }
> ```

## Text

Nuxt UI provides Tailwind CSS utility classes for text colors, so you can use class names like `text-dimmed` or `text-muted`:

```vue
<template>
  <span class="text-dimmed">Dimmed</span>
  <span class="text-muted">Muted</span>
  <span class="text-toned">Toned</span>
  <span class="text-default">Text</span>
  <span class="text-highlighted">Highlighted</span>
  <span class="text-inverted bg-inverted">Inverted</span>
</template>
```

Each utility class uses a CSS variable to set its color for light and dark modes:

```css [Light]
:root {
  --ui-text-dimmed: var(--ui-color-neutral-400);
  --ui-text-muted: var(--ui-color-neutral-500);
  --ui-text-toned: var(--ui-color-neutral-600);
  --ui-text: var(--ui-color-neutral-700);
  --ui-text-highlighted: var(--ui-color-neutral-900);
  --ui-text-inverted: white;
}
```

```css [Dark]
.dark {
  --ui-text-dimmed: var(--ui-color-neutral-500);
  --ui-text-muted: var(--ui-color-neutral-400);
  --ui-text-toned: var(--ui-color-neutral-300);
  --ui-text: var(--ui-color-neutral-200);
  --ui-text-highlighted: white;
  --ui-text-inverted: var(--ui-color-neutral-900);
}
```

> [!TIP]
> 
> You can customize these CSS variables in your `main.css` file:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-text: var(--ui-color-neutral-900);
> }
> 
> .dark {
>   --ui-text: white;
> }
> ```
> 
> To change several of these variables at once, [assign a different color to `neutral`](https://ui.nuxt.com/docs/getting-started/theme/design-system#runtime-configuration) in your config instead.

## Background

Nuxt UI provides Tailwind CSS utility classes for background colors, so you can use class names like `bg-default` or `bg-muted`:

```vue
<template>
  <div class="bg-default">Default</div>
  <div class="bg-muted">Muted</div>
  <div class="bg-elevated">Elevated</div>
  <div class="bg-accented">Accented</div>
  <div class="bg-inverted text-inverted">Inverted</div>
</template>
```

Each utility class uses a CSS variable to set its color for light and dark modes:

```css [Light]
:root {
  --ui-bg: white;
  --ui-bg-muted: var(--ui-color-neutral-50);
  --ui-bg-elevated: var(--ui-color-neutral-100);
  --ui-bg-accented: var(--ui-color-neutral-200);
  --ui-bg-inverted: var(--ui-color-neutral-900);
}
```

```css [Dark]
.dark {
  --ui-bg: var(--ui-color-neutral-900);
  --ui-bg-muted: var(--ui-color-neutral-800);
  --ui-bg-elevated: var(--ui-color-neutral-800);
  --ui-bg-accented: var(--ui-color-neutral-700);
  --ui-bg-inverted: white;
}
```

> [!TIP]
> 
> You can customize these CSS variables in your `main.css` file:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-bg: var(--ui-color-neutral-50);
> }
> 
> .dark {
>   --ui-bg: var(--ui-color-neutral-950);
> }
> ```

## Border

Nuxt UI provides Tailwind CSS utility classes for border colors, so you can use class names like `border-default` or `border-muted`:

```vue
<template>
  <div class="border border-default">Default</div>
  <div class="border border-muted">Muted</div>
  <div class="border border-accented">Accented</div>
  <div class="border border-inverted">Inverted</div>
</template>
```

Each utility class uses a CSS variable to set its color for light and dark modes:

```css [Light]
:root {
  --ui-border: var(--ui-color-neutral-200);
  --ui-border-muted: var(--ui-color-neutral-200);
  --ui-border-accented: var(--ui-color-neutral-300);
  --ui-border-inverted: var(--ui-color-neutral-900);
}
```

```css [Dark]
.dark {
  --ui-border: var(--ui-color-neutral-800);
  --ui-border-muted: var(--ui-color-neutral-700);
  --ui-border-accented: var(--ui-color-neutral-700);
  --ui-border-inverted: white;
}
```

> [!TIP]
> 
> You can customize these CSS variables in your `main.css` file:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-border: var(--ui-color-neutral-100);
> }
> 
> .dark {
>   --ui-border: var(--ui-color-neutral-900);
> }
> ```

## Focus `4.9+`

Nuxt UI applies a `focus-visible` outline on every interactive element, tinted with the `color` prop of the component, e.g. `outline-primary/25` when `color="primary"` or `outline-inverted/25` when `color="neutral"`.

If you prefer a single outline color across your entire app regardless of the component color, you can add a global rule in your `main.css` file:

```css [Primary]
@import "tailwindcss";
@import "@nuxt/ui";

*,
::before,
::after {
  @apply outline-primary/25;
}

*:focus-visible,
*:has(> a:focus-visible) {
  --tw-ring-color: var(--ui-primary);
}
```

```css [Neutral]
@import "tailwindcss";
@import "@nuxt/ui";

*,
::before,
::after {
  @apply outline-inverted/25;
}

*:focus-visible,
*:has(> a:focus-visible) {
  --tw-ring-color: var(--ui-border-inverted);
}
```

The first rule tints the focus outline of every component, the second one aligns the ring recolor applied on focus by variants with a visible border like `outline` or `subtle`, including cards that highlight when their link is focused.

> [!NOTE]
> 
> Make sure to declare these rules outside of any `@layer` so they take precedence over the colors set by the component themes. Keep in mind the first rule also tints the native focus outline of your own elements, and variants recoloring a `border` on focus like [FileUpload](https://ui.nuxt.com/docs/components/file-upload) or [ContentSurround](https://ui.nuxt.com/docs/components/content-surround) keep following the component `color`.

## Radius

Nuxt UI overrides Tailwind CSS's default `rounded-*` utilities with a unified border radius system, so you can use regular [border radius utilities](https://tailwindcss.com/docs/border-radius) like `rounded-xs` or `rounded-2xl`:

```vue
<template>
  <div class="rounded-xs">xs</div>
  <div class="rounded-sm">sm</div>
  <div class="rounded-md">md</div>
  <div class="rounded-lg">lg</div>
  <div class="rounded-xl">xl</div>
  <div class="rounded-2xl">2xl</div>
  <div class="rounded-3xl">3xl</div>
</template>
```

These utility classes are calculated based on a global `--ui-radius` CSS variable, which defines the base radius value applied across all components for a consistent look.

```css
:root {
  --ui-radius: 0.25rem;
}
```

Each utility multiplies this base value:

```css
@theme {
  --radius-xs: calc(var(--ui-radius) * 0.5);
  --radius-sm: var(--ui-radius);
  --radius-md: calc(var(--ui-radius) * 1.5);
  --radius-lg: calc(var(--ui-radius) * 2);
  --radius-xl: calc(var(--ui-radius) * 3);
  --radius-2xl: calc(var(--ui-radius) * 4);
  --radius-3xl: calc(var(--ui-radius) * 6);
}
```

> [!TIP]
> 
> You can customize the base radius value in your `main.css` file:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-radius: 0.5rem;
> }
> ```

> [!WARNING]
> 
> Changing `--ui-radius` also affects the `rounded-xs` to `rounded-3xl` classes on your own markup and third-party components, not only Nuxt UI components.

> [!NOTE]
> 
> Try the  theme picker in the header above to change the base radius value.

## Container

Nuxt UI provides a `--ui-container` CSS variable that controls the maximum width of the [Container](https://ui.nuxt.com/docs/components/container) component.

```css
:root {
  --ui-container: 80rem; /* var(--container-7xl) */
}
```

> [!TIP]
> 
> You can customize this value in your `main.css` file to adjust container widths consistently throughout your application:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> @theme {
>   --container-8xl: 90rem;
> }
> 
> :root {
>   --ui-container: var(--container-8xl);
> }
> ```

## Header

Nuxt UI provides a `--ui-header-height` CSS variable that controls the height of the [Header](https://ui.nuxt.com/docs/components/header) component.

```css
:root {
  --ui-header-height: 4rem;
}
```

> [!TIP]
> 
> You can customize this value in your `main.css` to adjust header height consistently throughout your application:
> 
> ```css [app/assets/css/main.css]
> @import "tailwindcss";
> @import "@nuxt/ui";
> 
> :root {
>   --ui-header-height: --spacing(24);
> }
> ```

## Body

Nuxt UI applies default classes on the `<body>` element of your app for consistent theming across light and dark modes:

```css
body {
  @apply antialiased text-default bg-default scheme-light dark:scheme-dark;
}
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
