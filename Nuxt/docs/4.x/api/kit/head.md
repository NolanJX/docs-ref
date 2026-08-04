# Head

> Nuxt Kit provides utilities to help you manage head configuration in modules.

## `setGlobalHead`

Sets global head configuration for your Nuxt application. This utility allows modules to programmatically configure meta tags, links, scripts, and other head elements that will be applied across all pages.

The provided head configuration will be merged with any existing head configuration using deep merging, with your provided values taking precedence.

<tip>

This is particularly useful for modules that need to inject global meta tags, stylesheets, or scripts into the application head.

</tip>

### Type

```tstwoslash
// @errors: 2391
// ---cut---
import type { SerializableHead } from '@unhead/vue/types'

interface AppHeadMetaObject extends SerializableHead {
  charset?: string
  viewport?: string
}

function setGlobalHead (head: AppHeadMetaObject): void
```

### Parameters

#### `head`

**Type**: `AppHeadMetaObject`

An object containing head configuration. All properties are optional and will be merged with existing configuration:

- `charset`: Character encoding for the document
- `viewport`: Viewport meta tag configuration
- `meta`: Array of meta tag objects
- `link`: Array of link tag objects (stylesheets, icons, etc.)
- `style`: Array of inline style tag objects
- `script`: Array of script tag objects
- `noscript`: Array of noscript tag objects
- `title`: Default page title
- `titleTemplate`: Template for formatting page titles
- `bodyAttrs`: Attributes to add to the `<body>` tag
- `htmlAttrs`: Attributes to add to the `<html>` tag

### Example

#### Adding Global Meta Tags

```ts
import { defineNuxtModule, setGlobalHead } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    setGlobalHead({
      meta: [
        { name: 'theme-color', content: '#ffffff' },
        { name: 'author', content: 'Your Name' },
      ],
    })
  },
})
```

#### Injecting Global Stylesheets

```ts
import { defineNuxtModule, setGlobalHead } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    setGlobalHead({
      link: [
        {
          rel: 'stylesheet',
          href: 'https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap',
        },
      ],
    })
  },
})
```

#### Adding Global Scripts

```ts
import { defineNuxtModule, setGlobalHead } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    setGlobalHead({
      script: [
        {
          src: 'https://cdn.example.com/analytics.js',
          async: true,
          defer: true,
        },
      ],
    })
  },
})
```

#### Setting HTML Attributes

```ts
import { defineNuxtModule, setGlobalHead } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    setGlobalHead({
      htmlAttrs: {
        lang: 'en',
        dir: 'ltr',
      },
      bodyAttrs: {
        class: 'custom-body-class',
      },
    })
  },
})
```

<style>

html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .sbKd-, html code.shiki .sbKd-{--shiki-light:#FF5370;--shiki-default:#FF5370;--shiki-dark:#FF9CAC}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/kit/src/head.ts)
