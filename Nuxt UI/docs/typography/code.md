---
title: "Code"
description: "Display inline code and syntax-highlighted code blocks with copy-to-clipboard support."
canonical_url: "https://ui.nuxt.com/docs/typography/code"
---
# Code

> Display inline code and syntax-highlighted code blocks with copy-to-clipboard support.

## Code blocks

Code blocks are rendered by the `ProsePre` component with syntax highlighting powered by [Shiki](https://github.com/shikijs/shiki).

````mdc
```ts
export default defineNuxtConfig({
  modules: ['@nuxt/ui']
})
```
````

```ts
/**
 * Props for the ProseCode component
 */
interface ProseCodeProps {
  lang?: string | undefined;
  /**
   * @default 'neutral'
   */
  color?: "error" | "primary" | "secondary" | "success" | "info" | "warning" | "neutral" | undefined;
  ui?: { base?: any; } | undefined;
}
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      pre: {
        slots: {
          root: 'relative my-5 group',
          header: 'flex items-center gap-1.5 border border-muted bg-default border-b-0 relative rounded-t-md px-4 py-3',
          filename: 'text-default text-sm/6',
          icon: 'size-4 shrink-0',
          copy: 'absolute top-[11px] end-[11px] lg:opacity-0 lg:group-hover:opacity-100 lg:focus-visible:opacity-100 transition',
          base: 'group font-mono text-sm/6 border border-muted bg-muted rounded-md px-4 py-3 whitespace-pre-wrap wrap-break-word overflow-x-auto outline-primary/25 focus-visible:outline-3 focus-visible:border-primary **:[.line]:block **:[.line.highlight]:-mx-4 **:[.line.highlight]:px-4 **:[.line.highlight]:bg-accented/50!'
        },
        variants: {
          filename: {
            true: {
              root: '[&>pre]:rounded-t-none [&>pre]:my-0 my-5'
            }
          }
        }
      }
    }
  }
})
```

### Language

Syntax highlighting is available for dozens of programming languages.

````html
```vue
<script setup lang="ts">
const message = ref('Hello World!')

function updateMessage() {
  message.value = 'Button clicked!'
}
</script>

<template>
  <div>
    <h1>{{ message }}</h1>
    <UButton @click="updateMessage">
      Click me
    </UButton>
  </div>
</template>
```
````

> [!TIP]
> 
> By default, `material-theme-lighter` and `material-theme-palenight` Shiki themes are used for light and dark mode respectively. When using `@nuxt/content`, you can change this through the [`content.build.markdown.highlight`](https://content.nuxt.com/docs/getting-started/configuration#highlight) key in your `nuxt.config.ts`. When using standalone `@nuxtjs/mdc`, configure themes through the [`mdc.highlight`](https://github.com/nuxt-content/mdc#configurations) key. When using [Comark](https://comark.dev), configure themes through the `shiki` plugin options.
> 
> To support dark mode with Comark, add the following CSS to your stylesheet:
> 
> ```css [main.css]
> html.dark .shiki span {
>   color: var(--shiki-dark) !important;
>   background-color: var(--shiki-dark-bg) !important;
>   font-style: var(--shiki-dark-font-style) !important;
>   font-weight: var(--shiki-dark-font-weight) !important;
>   text-decoration: var(--shiki-dark-text-decoration) !important;
> }
> ```

### Filename

Code blocks support filename display with automatic icon detection.

````mdc
```ts [nuxt.config.ts]
export default defineNuxtConfig({
  modules: ['@nuxt/ui']
})
```
````

> [!TIP]
> 
> The filename icon is rendered by the `ProseCodeIcon` component and contains a set of predefined icons which you can customize in your app configuration:
> 
> ```ts [app/app.config.ts]
> export default defineAppConfig({
>   ui: {
>     prose: {
>       codeIcon: {
>         terminal: 'i-ph-terminal-window-duotone',
>         config: 'i-lucide-settings',
>         package: 'i-lucide-package'
>       }
>     }
>   }
> })
> ```
> 
> ```ts [vite.config.ts]
> import { defineConfig } from 'vite'
> import vue from '@vitejs/plugin-vue'
> import ui from '@nuxt/ui/vite'
> 
> export default defineConfig({
>   plugins: [
>     vue(),
>     ui({
>       ui: {
>         prose: {
>           codeIcon: {
>             terminal: 'i-ph-terminal-window-duotone',
>             config: 'i-lucide-settings',
>             package: 'i-lucide-package'
>           }
>         }
>       }
>     })
>   ]
> })
> ```

> [!NOTE]
> 
> These filename icons come from the [`vscode-icons`](https://icones.js.org/collection/vscode-icons) collection, which is separate from the `lucide` icons the rest of the components use, and are resolved on demand from the file extension. Install `@iconify-json/vscode-icons` to serve them locally and offline, just like any other collection.

### Copy button

Every code-block has a built-in copy button that will copy the code to your clipboard.

Set the `copy` prop to `false` to hide the button, or pass [Button](https://ui.nuxt.com/docs/components/button) props to customize it.

> [!TIP]
> 
> You can change the icon through the `ui.icons.copy` and `ui.icons.copyCheck` keys in your app configuration:
> 
> ```ts [app/app.config.ts]
> export default defineAppConfig({
>   ui: {
>     icons: {
>       copy: 'i-lucide-copy',
>       copyCheck: 'i-lucide-copy-check'
>     }
>   }
> })
> ```
> 
> ```ts [vite.config.ts]
> import { defineConfig } from 'vite'
> import vue from '@vitejs/plugin-vue'
> import ui from '@nuxt/ui/vite'
> 
> export default defineConfig({
>   plugins: [
>     vue(),
>     ui({
>       ui: {
>         icons: {
>           copy: 'i-lucide-copy',
>           copyCheck: 'i-lucide-copy-check'
>         }
>       }
>     })
>   ]
> })
> ```

### Line highlighting

Highlight specific lines to draw attention to important parts.

````mdc
```ts [nuxt.config.ts] {2}
export default defineNuxtConfig({
  modules: ['@nuxt/ui'], // This line is highlighted
  css: ['~/assets/css/main.css']
})
```
````

### Code diff

Use the `diff` language to show changes between code versions.

````mdc
```diff [nuxt.config.ts]
export default defineNuxtConfig({
  modules: [
-   '@nuxt/ui-pro'
+   '@nuxt/ui'
  ]
})
```
````

## Inline code

Inline code snippets are rendered by the `ProseCode` component.

```vue
<template>
  <p>
    <code>
      inline code
    </code>
  </p>
</template>
```

```mdc
`inline code`
```

:::

```ts
/**
 * Props for the ProseCode component
 */
interface ProseCodeProps {
  lang?: string | undefined;
  /**
   * @default 'neutral'
   */
  color?: "error" | "primary" | "secondary" | "success" | "info" | "warning" | "neutral" | undefined;
  ui?: { base?: any; } | undefined;
}
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      code: {
        base: 'px-1.5 py-0.5 text-sm font-mono font-medium rounded-md inline-block',
        variants: {
          color: {
            primary: 'border border-primary/25 bg-primary/10 text-primary',
            secondary: 'border border-secondary/25 bg-secondary/10 text-secondary',
            success: 'border border-success/25 bg-success/10 text-success',
            info: 'border border-info/25 bg-info/10 text-info',
            warning: 'border border-warning/25 bg-warning/10 text-warning',
            error: 'border border-error/25 bg-error/10 text-error',
            neutral: 'border border-muted text-highlighted bg-muted'
          }
        },
        defaultVariants: {
          color: 'neutral'
        }
      }
    }
  }
})
```

### Color

Use the `color` prop to change the color of the inline code. Defaults to `neutral`.

```mdc
`inline code`{color="error"}
```

### Lang

Use the `lang` prop to specify the language of the inline code.

```mdc
`nuxt.config.ts`{lang="ts-type"}
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
