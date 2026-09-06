---
title: "Design System"
description: "Nuxt UI's design system uses Tailwind CSS for simple theming and easy customization."
canonical_url: "https://ui.nuxt.com/docs/getting-started/theme/design-system"
---
# Design System

> Nuxt UI's design system uses Tailwind CSS for simple theming and easy customization.

## Tailwind CSS

Tailwind CSS uses a CSS-first configuration, letting you define your design tokens with the [`@theme`](https://tailwindcss.com/docs/functions-and-directives#theme-directive) directive directly in your CSS. This makes your theme portable, maintainable and easy to customize.

```css [app/assets/css/main.css]
@import "tailwindcss";
@import "@nuxt/ui";

@theme {
  /* Your custom design tokens go here */
}
```

> [!NOTE]
> See: https://tailwindcss.com/docs/theme
> 
> Check the Tailwind CSS documentation for all available theme variable customization options.

> [!TIP]
> 
> Tailwind CSS v4 changed its [Preflight](https://tailwindcss.com/docs/upgrade-guide#buttons-use-the-default-cursor) so that buttons use `cursor: default` instead of `cursor: pointer` to match browser defaults. If you'd like to restore the pointer cursor globally, add these base styles to your CSS:
> 
> ```css [app/assets/css/main.css]
> @layer base {
>   button:not(:disabled),
>   [role="button"]:not(:disabled) {
>     cursor: pointer;
>   }
> }
> ```

### Fonts

Use the `--font-*` theme variables to [customize the font family utilities](https://tailwindcss.com/docs/font-family#customizing-your-theme) in your project.

```css [app/assets/css/main.css]
@import "tailwindcss";
@import "@nuxt/ui";

@theme {
  --font-sans: 'Public Sans', system-ui, sans-serif;
  --font-mono: 'JetBrains Mono', monospace;
}
```

**Nuxt:**

> [!NOTE]
> See: /docs/getting-started/integrations/fonts
> 
> Fonts defined here are automatically loaded and optimized by the `@nuxt/fonts` module.

### Colors

Use the `--color-*` theme variables to [customize your colors](https://tailwindcss.com/docs/colors#customizing-your-colors) or [override default colors](https://tailwindcss.com/docs/colors#overriding-default-colors).

```css [app/assets/css/main.css]
@import "tailwindcss";
@import "@nuxt/ui";

@theme static {
  /* Override default green color */
  --color-green-50: #EFFDF5;
  --color-green-100: #D9FBE8;
  --color-green-200: #B3F5D1;
  --color-green-300: #75EDAE;
  --color-green-400: #00DC82;
  --color-green-500: #00C16A;
  --color-green-600: #00A155;
  --color-green-700: #007F45;
  --color-green-800: #016538;
  --color-green-900: #0A5331;
  --color-green-950: #052E16;

  /* Define new custom color */
  --color-brand-50: #fef2f2;
  --color-brand-100: #fee2e2;
  --color-brand-200: #fecaca;
  --color-brand-300: #fca5a5;
  --color-brand-400: #f87171;
  --color-brand-500: #ef4444;
  --color-brand-600: #dc2626;
  --color-brand-700: #b91c1c;
  --color-brand-800: #991b1b;
  --color-brand-900: #7f1d1d;
  --color-brand-950: #450a0a;
}
```

> [!WARNING]
> 
> When adding custom colors, make sure to define all shades from `50` to `950` for each color.

### Breakpoints

Use the `--breakpoint-*` theme variables to [customize your breakpoints](https://tailwindcss.com/docs/responsive-design#customizing-your-theme).

```css [app/assets/css/main.css]
@import "tailwindcss";
@import "@nuxt/ui";

@theme {
  --breakpoint-3xl: 1920px;
  --breakpoint-4xl: 2560px;
  --breakpoint-5xl: 3840px;
}
```

### Motion

Use the `--ease-*` theme variables to [customize the easing](https://tailwindcss.com/docs/transition-timing-function#customizing-your-theme) of the animations and movement transitions in your project.

```css [app/assets/css/main.css]
@import "tailwindcss";
@import "@nuxt/ui";

@theme {
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
}
```

Enter and exit animations run on `--ease-out`, indeterminate loading animations on `--ease-in-out`, so overriding these two retimes the library.

> [!NOTE]
> 
> When `prefers-reduced-motion` is set, overlays fade in place instead of scaling or sliding and movement transitions are disabled.

## Color System

Nuxt UI's color system is designed around **semantic naming** rather than specific color values. This approach makes your UI more maintainable and makes theme switching easy.

### Semantic colors

Nuxt UI provides semantic color aliases that describe the **purpose** of the color. Each alias is defined based on a color from your `@theme` configuration, which can be any color you define in addition to the [default Tailwind CSS palette](https://tailwindcss.com/docs/colors).

| Color | Default | Description |
| --- | --- | --- |
| `primary` | `green` | Main CTAs, active navigation, brand elements, important links |
| `secondary` | `blue` | Secondary buttons, alternative actions, complementary UI elements |
| `success` | `green` | Success messages, completed states, positive confirmations |
| `info` | `blue` | Info alerts, tooltips, help text, neutral notifications |
| `warning` | `yellow` | Warning messages, pending states, attention-needed items |
| `error` | `red` | Error messages, validation errors, destructive actions |
| `neutral` | `slate` | Text, borders, backgrounds, disabled states |

These semantic colors are available in the `color` prop of Nuxt UI components:

```vue
<template>
  <UButton color="primary">
    Save Changes
  </UButton>
</template>
```

> [!NOTE]
> 
> Try the  theme picker in the header to instantly see how different color schemes affect the entire UI!

### Runtime configuration

**Nuxt:**

You can configure these colors at runtime in your [`app.config.ts`](https://nuxt.com/docs/4.x/directory-structure/app/app-config) file under the `ui.colors` key, allowing for dynamic theme customization without restarting the server:

```ts [app/app.config.ts]
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'blue',
      secondary: 'purple',
      neutral: 'zinc'
    }
  }
})
```

**Vue:**

You can configure these colors at runtime in your `vite.config.ts` file under the `ui.colors` key, allowing for dynamic theme customization:

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      ui: {
        colors: {
          primary: 'blue',
          secondary: 'purple',
          neutral: 'zinc'
        }
      }
    })
  ]
})
```

> [!CAUTION]
> 
> You can only use colors that exist in your theme. Either:
> 
> - Use [Tailwind's default colors](https://tailwindcss.com/docs/colors) (like `blue`, `green`, `zinc`)
> - Define custom colors first using the `@theme` directive (like `brand` in our example above)

### Extend colors

You may want to define extra semantic colors beyond the defaults, such as adding a `tertiary` color:

**Nuxt:**

First, register the new color in your `nuxt.config.ts` under the `ui.theme.colors` key:

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  ui: {
    theme: {
      colors: [
        'primary',
        'secondary',
        'tertiary',
        'info',
        'success',
        'warning',
        'error'
      ]
    }
  }
})
```

Then, assign it in your `app.config.ts` under the `ui.colors` key:

```ts [app/app.config.ts]
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'blue',
      secondary: 'purple',
      tertiary: 'indigo'
    }
  }
})
```

**Vue:**

Register and assign the new color in your `vite.config.ts` file:

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      theme: {
        colors: [
          'primary',
          'secondary',
          'tertiary',
          'info',
          'success',
          'warning',
          'error'
        ]
      },
      ui: {
        colors: {
          primary: 'blue',
          secondary: 'purple',
          tertiary: 'indigo'
        }
      }
    })
  ]
})
```

Finally, use this new color in components that support the `color` prop or [as a class](https://ui.nuxt.com/docs/getting-started/theme/css-variables):

```vue
<UButton color="tertiary">
  Special Action
</UButton>
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
