---
title: "Installation"
description: "Learn how to install and configure Nuxt UI in your Vue application, compatible with both plain Vite and Inertia."
canonical_url: "https://ui.nuxt.com/docs/getting-started/installation/vue"
---
# Installation

> Learn how to install and configure Nuxt UI in your Vue application, compatible with both plain Vite and Inertia.

> [!NOTE]
> See: /docs/getting-started/installation/nuxt
> 
> Looking for the **Nuxt** version?

## Setup

### Add to a Vue project

#### Install the Nuxt UI package

```bash [pnpm]
pnpm add @nuxt/ui tailwindcss
```

```bash [yarn]
yarn add @nuxt/ui tailwindcss
```

```bash [npm]
npm install @nuxt/ui tailwindcss
```

```bash [bun]
bun add @nuxt/ui tailwindcss
```

#### Add the Nuxt UI Vite plugin in your `vite.config.ts`

```ts [vite.config.ts (Vite)]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui()
  ]
})
```

```ts [vite.config.ts (Laravel Inertia)]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'
import laravel from 'laravel-vite-plugin'

export default defineConfig({
  plugins: [
    laravel({
      input: ['resources/js/app.ts'],
      refresh: true
    }),
    vue({
      template: {
        transformAssetUrls: {
          base: null,
          includeAbsolute: false
        }
      }
    }),
    ui({
      router: 'inertia'
    })
  ]
})
```

```ts [vite.config.ts (AdonisJS Inertia)]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'
import adonisjs from '@adonisjs/vite/client'
import inertia from '@adonisjs/inertia/client'

export default defineConfig({
  plugins: [
    adonisjs({
      entrypoints: ['inertia/app/app.ts'],
      reload: ['resources/views/**/*.edge']
    }),
    inertia(),
    vue(),
    ui({
      router: 'inertia'
    })
  ]
})
```

> [!TIP]
> 
> Nuxt UI registers `unplugin-auto-import` and `unplugin-vue-components`, which will generate `auto-imports.d.ts` and `components.d.ts` type declaration files. You will likely want to gitignore these, and add them to your `tsconfig`.
> 
> ```json [tsconfig.app.json]
> {
>   "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue", "auto-imports.d.ts", "components.d.ts"]
> }
> ```
> 
> ```bash [.gitignore]
> # Auto-generated type declarations
> auto-imports.d.ts
> components.d.ts
> ```

> [!WARNING]
> 
> These declaration files are only written when Vite runs. The default `create-vue` `build` script runs `vue-tsc` in parallel with `vite build` (`run-p type-check "build-only {@}"`), so on a clean checkout type-checking can start before the files exist and fail with errors like `Cannot find name 'useToast'`. Run the type-check after the build instead so the declarations are generated first.
> 
> ```json [package.json]
> {
>   "scripts": {
>     "build": "run-s build-only type-check"
>   }
> }
> ```

> [!TIP]
> 
> Internally, Nuxt UI relies on custom aliases to resolve the theme types. If you're using TypeScript, add these aliases to your `tsconfig` files to enable auto-completion in your `vite.config.ts`.
> 
> ```json [tsconfig.node.json]
> {
>   "compilerOptions": {
>     "paths": {
>       "#build/ui": [
>         "./node_modules/.nuxt-ui/ui"
>       ]
>     }
>   }
> }
> ```
> 
> ```json [tsconfig.app.json]
> {
>   "compilerOptions": {
>     "paths": {
>       "#build/ui/*": [
>         "./node_modules/.nuxt-ui/ui/*"
>       ]
>     }
>   }
> }
> ```

#### Use the Nuxt UI Vue plugin

```ts [src/main.ts (Vite)]
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'
import ui from '@nuxt/ui/vue-plugin'
import App from './App.vue'

const app = createApp(App)

const router = createRouter({
  routes: [],
  history: createWebHistory()
})

app.use(router)
app.use(ui)

app.mount('#app')
```

```ts [resources/js/app.ts (Laravel Inertia)]
import type { DefineComponent } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'
import ui from '@nuxt/ui/vue-plugin'
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers'
import { createApp, h } from 'vue'

const appName = import.meta.env.VITE_APP_NAME || 'Laravel x Nuxt UI'

createInertiaApp({
  title: title => (title ? `${title} - ${appName}` : appName),
  resolve: name =>
    resolvePageComponent(
      `./pages/${name}.vue`,
      import.meta.glob<DefineComponent>('./pages/**/*.vue')
    ),
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .use(ui)
      .mount(el)
  }
})
```

```ts [inertia/app/app.ts (AdonisJS Inertia)]
import type { DefineComponent } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'
import ui from '@nuxt/ui/vue-plugin'
import { resolvePageComponent } from '@adonisjs/inertia/helpers'
import { createApp, h } from 'vue'

const appName = import.meta.env.VITE_APP_NAME || 'AdonisJS x Nuxt UI'

createInertiaApp({
  title: title => (title ? `${title} - ${appName}` : appName),
  resolve: name =>
    resolvePageComponent(
      `../pages/${name}.vue`,
      import.meta.glob<DefineComponent>('../pages/**/*.vue')
    ),
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .use(ui)
      .mount(el)
  }
})
```

#### Import Tailwind CSS and Nuxt UI in your CSS

```css [src/assets/css/main.css (Vite)]
@import "tailwindcss";
@import "@nuxt/ui";
```

```css [resources/css/app.css (Laravel Inertia)]
@import "tailwindcss";
@import "@nuxt/ui";
```

```css [inertia/css/app.css (AdonisJS Inertia)]
@import "tailwindcss";
@import "@nuxt/ui";
```

> [!TIP]
> 
> Import the CSS file in your entrypoint.
> 
> ```ts [src/main.ts (Vite)]
> import './assets/css/main.css'
> 
> import { createApp } from 'vue'
> import { createRouter, createWebHistory } from 'vue-router'
> import ui from '@nuxt/ui/vue-plugin'
> import App from './App.vue'
> 
> const app = createApp(App)
> 
> const router = createRouter({
>   routes: [],
>   history: createWebHistory()
> })
> 
> app.use(router)
> app.use(ui)
> 
> app.mount('#app')
> ```
> 
> ```ts [resources/js/app.ts (Laravel Inertia)]
> import '../css/app.css'
> import type { DefineComponent } from 'vue'
> import { createInertiaApp } from '@inertiajs/vue3'
> import ui from '@nuxt/ui/vue-plugin'
> import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers'
> import { createApp, h } from 'vue'
> 
> const appName = import.meta.env.VITE_APP_NAME || 'Laravel x Nuxt UI'
> 
> createInertiaApp({
>   title: title => (title ? `${title} - ${appName}` : appName),
>   resolve: name =>
>     resolvePageComponent(
>       `./pages/${name}.vue`,
>       import.meta.glob<DefineComponent>('./pages/**/*.vue')
>     ),
>   setup({ el, App, props, plugin }) {
>     createApp({ render: () => h(App, props) })
>       .use(plugin)
>       .use(ui)
>       .mount(el)
>   }
> })
> ```
> 
> ```ts [inertia/app/app.ts (AdonisJS Inertia)]
> import '../css/app.css'
> import type { DefineComponent } from 'vue'
> import { createInertiaApp } from '@inertiajs/vue3'
> import ui from '@nuxt/ui/vue-plugin'
> import { resolvePageComponent } from '@adonisjs/inertia/helpers'
> import { createApp, h } from 'vue'
> 
> const appName = import.meta.env.VITE_APP_NAME || 'AdonisJS x Nuxt UI'
> 
> createInertiaApp({
>   title: title => (title ? `${title} - ${appName}` : appName),
>   resolve: name =>
>     resolvePageComponent(
>       `../pages/${name}.vue`,
>       import.meta.glob<DefineComponent>('../pages/**/*.vue')
>     ),
>   setup({ el, App, props, plugin }) {
>     createApp({ render: () => h(App, props) })
>       .use(plugin)
>       .use(ui)
>       .mount(el)
>   }
> })
> ```

> [!NOTE]
> 
> It's recommended to install the [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss) extension for VSCode and add the following settings:
> 
> ```json [.vscode/settings.json]
> {
>   "files.associations": {
>     "*.css": "tailwindcss"
>   },
>   "editor.quickSuggestions": {
>     "strings": "on"
>   },
>   "tailwindCSS.classAttributes": ["class", "ui"],
>   "tailwindCSS.classFunctions": ["defineAppConfig"]
> }
> ```

#### Wrap your app with App component

```vue [src/App.vue (Vite)]
<template>
  <UApp>
    <RouterView />
  </UApp>
</template>
```

```vue [resources/js/pages/index.vue (Laravel Inertia)]
<template>
  <UApp>
    <!-- Your content goes here -->
  </UApp>
</template>
```

```vue [inertia/pages/index.vue (AdonisJS Inertia)]
<template>
  <UApp>
    <!-- Your content goes here -->
  </UApp>
</template>
```

> [!NOTE]
> See: /docs/components/app
> 
> The `App` component sets up global config and is required for **Toast**, **Tooltip** and **programmatic overlays**.

#### Add the `isolate` class to your root container

```html [index.html (Vite)]
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Nuxt UI</title>
  </head>
  <body>
    <div id="app" class="isolate"></div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

```blade [resources/views/app.blade.php (Laravel Inertia)]
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1">
    @inertiaHead
    @vite('resources/js/app.ts')
  </head>
  <body>
    <div class="isolate">
        @inertia
    </div>
  </body>
</html>
```

```edge [resources/views/inertia_layout.edge (AdonisJS Inertia)]
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1">
    @inertiaHead()
    @vite(['inertia/app/app.ts', `inertia/pages/${page.component}.vue`])
  </head>
  <body>
    @inertia({ class: 'isolate' })
  </body>
</html>
```

> [!NOTE]
> 
> This ensures styles are scoped to your app and prevents issues with overlays and stacking contexts.

### Use a Vue template

Get started with one of our [official templates](https://ui.nuxt.com/templates) by using the `Use this template` button on GitHub or the CLI:

```bash [Starter]
npm create nuxt@latest -- --no-modules -t ui-vue
```

```bash [Dashboard]
npm create nuxt@latest -- --no-modules -t ui-vue/dashboard
```

```bash [Chat]
npm create nuxt@latest -- --no-modules -t ui-vue/chat
```

[**Starter**](https://github.com/nuxt-ui-templates/starter-vue)

A minimal template to get started with Nuxt UI.

[**Dashboard**](https://github.com/nuxt-ui-templates/dashboard-vue)

A dashboard template with multi-column layout for building sophisticated admin interfaces.

[**Chat**](https://github.com/nuxt-ui-templates/chat-vue)

An AI chatbot template to build your own chatbot powered by Vercel AI SDK.

[**Starter Adonis**](https://github.com/nuxt-ui-templates/starter-adonis)

A minimal Nuxt UI template for AdonisJS using Inertia.js.

[**Starter Laravel**](https://github.com/nuxt-ui-templates/starter-laravel)

A minimal Nuxt UI template for Laravel using Inertia.js.

## Options

You can customize Nuxt UI by providing options in your `vite.config.ts`.

### `prefix`

Use the `prefix` option to change the prefix of the components.

- Default: `U`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      prefix: 'Nuxt'
    })
  ]
})
```

### `ui`

Use the `ui` option to provide configuration for Nuxt UI. This is the Vue equivalent of the `ui` key in Nuxt's `app.config.ts`.

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
          primary: 'green',
          neutral: 'slate'
        }
      }
    })
  ]
})
```

### `dts`

Use the `dts` option to enable or disable the generation of declaration files for auto-imported components and composables.

- Default: `true`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      dts: false
    })
  ]
})
```

### `icon`

Use the `icon` option to set default props for the [Icon](https://ui.nuxt.com/docs/components/icon) component (`size`, `mode`, `customize`) and to configure build-time icon bundling through `clientBundle`. Bundling of Nuxt UI's own icons is enabled by default when their collection is installed. Set `clientBundle: false` to opt out.

- Default: `{}`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      icon: {
        mode: 'svg',
        clientBundle: {
          scan: true
        }
      }
    })
  ]
})
```

> [!NOTE]
> See: /docs/getting-started/integrations/icons/vue#collections
> 
> Learn more about icon collections and client bundling in the **Icons** documentation.

### `colorMode`

Use the `colorMode` option to enable or disable the color mode integration from `@vueuse/core`.

- Default: `true`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      colorMode: false
    })
  ]
})
```

### `theme.colors`

Use the `theme.colors` option to define the dynamic color aliases used to generate components theme.

- Default: `['primary', 'secondary', 'success', 'info', 'warning', 'error']`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      theme: {
        colors: ['primary', 'error']
      }
    })
  ]
})
```

> [!NOTE]
> 
> Each alias generates a full set of `--ui-color-*` shades and variants in the component themes that have color variants, whether your app uses it or not. Trim the list to the aliases you actually use, and keep `error` if you use forms since validation styles rely on it.

> [!TIP]
> See: /docs/getting-started/theme/design-system#color-system
> 
> Learn more about color customization and theming in the Theme section.

### `theme.transitions`

Use the `theme.transitions` option to enable or disable transitions on components.

- Default: `true`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      theme: {
        transitions: false
      }
    })
  ]
})
```

> [!NOTE]
> 
> This option adds the `transition-colors` class on components with hover or active states.

### `theme.unstyled` `4.9+`

Use the `theme.unstyled` option to remove all default theme classes from components, keeping only their structure and the classes you provide through `class`, `ui` or `app.config.ui`.

- Default: `false`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      theme: {
        unstyled: true
      }
    })
  ]
})
```

> [!WARNING]
> 
> This strips **structural** classes too (positioning, transitions, flex/grid), not just cosmetic ones. Layout-heavy components like `Modal`, `Drawer` or `Calendar` will need you to re-supply their layout, similar to PrimeVue's unstyled mode.

### `theme.defaultVariants`

Use the `theme.defaultVariants` option to override the default `color` and `size` variants for components.

Only defaults that are exactly `primary` or `md` are replaced, so Avatar keeps `color: 'neutral'` and Separator keeps `size: 'xs'`.

- Default: `{ color: 'primary', size: 'md' }`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      theme: {
        defaultVariants: {
          color: 'neutral',
          size: 'sm'
        }
      }
    })
  ]
})
```

### `theme.prefix` `4.2+`

Use the `theme.prefix` option to configure the same prefix you set on your Tailwind CSS import. This ensures Nuxt UI components use the correct prefixed utility classes and CSS variables.

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      theme: {
        prefix: 'tw'
      }
    })
  ]
})
```

```css [src/assets/css/main.css]
@import "tailwindcss" prefix(tw);
@import "@nuxt/ui";
```

This will automatically prefix all Tailwind utility classes and CSS variables in Nuxt UI component themes:

```html
<!-- Without prefix -->
<button class="px-2 py-1 text-xs hover:bg-primary/75">Button</button>

<!-- With prefix: tw -->
<button class="tw:px-2 tw:py-1 tw:text-xs tw:hover:bg-primary/75">Button</button>
```

> [!NOTE]
> See: https://tailwindcss.com/docs/styling-with-utility-classes#using-the-prefix-option
> 
> Learn more about using a prefix in the Tailwind CSS documentation.

### `prose`

Use the `prose` option to enable Nuxt UI [`Prose` components](https://ui.nuxt.com/docs/typography) and their theme.

- Default: `false`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      prose: true
    })
  ]
})
```

### `autoImport`

Use the `autoImport` option to disable composable auto-imports or to customize [`unplugin-auto-import`](https://github.com/unplugin/unplugin-auto-import) options.

- Default: `{}`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      autoImport: false
    })
  ]
})
```

> [!NOTE]
> 
> When disabled, you can still import composables explicitly from `@nuxt/ui/composables`.

### `components`

Use the `components` option to disable component auto-imports or to customize [`unplugin-vue-components`](https://github.com/unplugin/unplugin-vue-components) options.

- Default: `{}`

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      components: false
    })
  ]
})
```

> [!NOTE]
> 
> When disabled, you can still import components explicitly, e.g. `import Button from '@nuxt/ui/components/Button.vue'` or `import ProseCode from '@nuxt/ui/components/prose/Code.vue'`.

### `router` `4.3+`

Use the `router` option to configure routing integration. This is useful for applications that don't use `vue-router`, such as Electron apps, MPAs, or frameworks like [Inertia.js](https://inertiajs.com/) or [Hybridly](https://hybridly.dev/).

- Default: `true`

| Value | Description |
| --- | --- |
| `true` | Uses `vue-router` for navigation with `RouterLink` component. |
| `false` | Disables routing integration, links render as plain `<a>` tags. |
| `'inertia'` | Uses Inertia.js for navigation with its `Link` component. |

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      router: false
    })
  ]
})
```

> [!TIP]
> 
> You can provide custom navigation logic for frameworks like **Hybridly** by setting `router: false` in the Vite config and passing a function when installing the Vue plugin:
> 
> ```ts [src/main.ts]
> import ui from '@nuxt/ui/vue-plugin'
> import { router } from 'hybridly'
> 
> app.use(ui, {
>   router: (event, { href, external }) => {
>     if (external) {
>       return
>     }
> 
>     event.preventDefault()
> 
>     router.navigate({ url: href })
>   }
> })
> ```

> [!NOTE]
> 
> When set to `false` or `'inertia'`, `vue-router` is not required as a dependency.

### `scanPackages` `4.3+`

Use the `scanPackages` option to specify additional npm packages that should be scanned for components using Nuxt UI. This is useful when you have a shared component library that uses Nuxt UI components internally.

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      scanPackages: ['@my-org/ui-components']
    })
  ]
})
```

> [!NOTE]
> 
> By default, only `@nuxt/ui` is scanned. Use this option when your external packages contain Vue components that use Nuxt UI.

### `root` `4.9+`

Use the `root` option to override the directory where Nuxt UI generates its `.nuxt-ui` directory (containing the theme templates). By default it uses Vite's `root`, but in setups like [`electron-vite`](https://electron-vite.org/) the renderer's `root` points to a sub-directory (e.g. `src/renderer`), so the templates land in `src/renderer/node_modules/.nuxt-ui` where Tailwind doesn't scan them, causing theme classes like `bg-default`, `ring-default` and `divide-default` to be missing.

```ts [electron.vite.config.ts]
import { defineConfig } from 'electron-vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  renderer: {
    root: 'src/renderer',
    plugins: [
      vue(),
      ui({
        root: __dirname
      })
    ]
  }
})
```

> [!NOTE]
> 
> Point `root` at your project root so the generated `.nuxt-ui` directory ends up in a `node_modules` that Tailwind scans.

### `experimental.componentDetection` `4.11+`

Use the `experimental.componentDetection` option to enable automatic component detection for tree-shaking. This feature scans your source code to detect which components are actually used and only generates the necessary CSS for those components (including their dependencies). Without it, the Vite plugin generates the theme CSS for every component. Detection covers the Vite root, the packages listed in [`scanPackages`](#scanpackages) and any `dirs` from the [`components`](#components) option located outside the root.

- Default: `false`
- Type: `boolean | string[]`

**Enable automatic detection:**

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      experimental: {
        componentDetection: true
      }
    })
  ]
})
```

**Include additional components for dynamic usage:**

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      experimental: {
        componentDetection: ['Modal', 'DropdownMenu', 'Popover']
      }
    })
  ]
})
```

> [!NOTE]
> 
> When providing an array of component names, automatic detection is enabled and these components (along with their dependencies) are guaranteed to be included. This is useful for dynamic components like `<component :is="..." />` that can't be statically analyzed.

> [!WARNING]
> 
> Newly used components are picked up on the next dev-server start. If you add a component and its styles are missing, restart the dev server.

## Continuous releases

Nuxt UI uses [pkg.pr.new](https://github.com/stackblitz-labs/pkg.pr.new) for continuous preview releases, providing developers with instant access to the latest features and bug fixes without waiting for official releases.

Automatic preview releases are created for all commits and PRs to the `v4` branch. Use them by replacing your package version with the specific commit hash or PR number.

```diff [package.json]
{
  "dependencies": {
-   "@nuxt/ui": "^4.0.0",
+   "@nuxt/ui": "https://pkg.pr.new/@nuxt/ui@4c96909",
  }
}
```

> [!NOTE]
> 
> **pkg.pr.new** will automatically comment on PRs with the installation URL, making it easy to test changes.

---

- [Template](https://codesandbox.io/p/devbox/nuxt-ui-vue-f2qp34)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
