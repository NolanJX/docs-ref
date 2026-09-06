---
title: "ProseCodeTree"
description: "Visualize file and folder structures with syntax-highlighted code."
canonical_url: "https://ui.nuxt.com/docs/typography/code-tree"
---
# ProseCodeTree

> Visualize file and folder structures with syntax-highlighted code.

## Usage

Wrap your code blocks with a `code-tree` component in any particular order to display a tree view of your files.

`````mdc
::code-tree{defaultValue="app/app.config.ts"}

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  modules: ['@nuxt/ui'],

  css: ['~/assets/css/main.css']
})

```

```css [app/assets/css/main.css]
@import "tailwindcss";
@import "@nuxt/ui";
```

```ts [app/app.config.ts]
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'sky',
      colors: 'slate'
    }
  }
})
```

```vue [app/app.vue]
<template>
  <UApp>
    <NuxtPage />
  </UApp>
</template>
```

```json [package.json]
{
  "name": "nuxt-app",
  "private": true,
  "type": "module",
  "scripts": {
    "build": "nuxt build",
    "dev": "nuxt dev",
    "generate": "nuxt generate",
    "preview": "nuxt preview",
    "postinstall": "nuxt prepare",
    "typecheck": "nuxt typecheck"
  },
  "dependencies": {
    "@iconify-json/lucide": "^1.2.0",
    "@nuxt/ui": "^4.0.0",
    "nuxt": "^4.0.0"
  },
  "devDependencies": {
    "typescript": "^6.0.0",
    "vue-tsc": "^3.2.0"
  }
}
```

```json [tsconfig.json]
{
  "extends": "./.nuxt/tsconfig.json"
}
```

````md [README.md]
# Nuxt 4 Minimal Starter

Look at the [Nuxt 4 documentation](https://nuxt.com/docs/getting-started/introduction) to learn more.

## Setup

Make sure to install the dependencies:

```bash
# npm
npm install

# pnpm
pnpm install

# yarn
yarn install

# bun
bun install
```

## Development server

Start the development server on `http://localhost:3000`:

```bash
# npm
npm run dev

# pnpm
pnpm run dev

# yarn
yarn dev

# bun
bun run dev
```

## Production

Build the application for production:

```bash
# npm
npm run build

# pnpm
pnpm run build

# yarn
yarn build

# bun
bun run build
```

Locally preview production build:

```bash
# npm
npm run preview

# pnpm
pnpm run preview

# yarn
yarn preview

# bun
bun run preview
```

Check out the [deployment documentation](https://nuxt.com/docs/getting-started/deployment) for more information.
````

::
`````

> [!NOTE]
> See: /docs/typography/code#code-blocks
> 
> Like the `ProsePre` component, the `CodeTree` handles filenames, icons and copy button.

## API

### Props

```ts
/**
 * Props for the ProseProseCodeTree component
 */
interface ProseProseCodeTreeProps {
  items?: TreeItem[] | undefined;
  /**
   * The selected path.
   */
  modelValue?: string | undefined;
  /**
   * The default path to select.
   */
  defaultValue?: string | undefined;
  /**
   * Expand all directories by default.
   * @default false
   */
  expandAll?: boolean | undefined;
  ui?: { root?: SlotClass; list?: SlotClass; item?: SlotClass; listWithChildren?: SlotClass; itemWithChildren?: SlotClass; link?: SlotClass; linkLeadingIcon?: SlotClass; linkLabel?: SlotClass; linkTrailing?: SlotClass; linkTrailingIcon?: SlotClass; content?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseCodeTree component
 */
interface ProseCodeTreeSlots {
  default(): any;
}
```

## Theme

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {}
  }
})
```

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseCodeTree.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-code-tree.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/CodeTree.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
