# Context

> Nuxt Kit provides a set of utilities to help you work with context.

Nuxt modules allow you to enhance Nuxt's capabilities. They offer a structured way to keep your code organized and modular. If you're looking to break down your module into smaller components, Nuxt offers the `useNuxt` and `tryUseNuxt` functions. These functions enable you to conveniently access the Nuxt instance from the context without having to pass it as an argument.

<note>

When you're working with the `setup` function in Nuxt modules, Nuxt is already provided as the second argument. This means you can access it directly without needing to call `useNuxt()`.

</note>

## `useNuxt`

Get the Nuxt instance from the context. It will throw an error if Nuxt is not available.

### Usage

```ts
import { useNuxt } from '@nuxt/kit'

const setupSomeFeature = () => {
  const nuxt = useNuxt()

  // You can now use the nuxt instance
  console.log(nuxt.options)
}
```

### Type

```tstwoslash
// @errors: 2391
import type { Nuxt } from '@nuxt/schema'
// ---cut---
function useNuxt (): Nuxt
```

### Return Value

The `useNuxt` function returns the Nuxt instance, which contains all the options and methods available in Nuxt.

<table>
<thead>
  <tr>
    <th>
      Property
    </th>
    
    <th>
      Type
    </th>
    
    <th>
      Description
    </th>
  </tr>
</thead>

<tbody>
  <tr>
    <td>
      <code>
        options
      </code>
    </td>
    
    <td>
      <code>
        NuxtOptions
      </code>
    </td>
    
    <td>
      The resolved Nuxt configuration.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        hooks
      </code>
    </td>
    
    <td>
      <code>
        Hookable<NuxtHooks>
      </code>
    </td>
    
    <td>
      The Nuxt hook system. Allows registering and listening to lifecycle events.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        hook
      </code>
    </td>
    
    <td>
      <code>
        (name: string, (...args: any[]) => Promise<void> | void) => () => void
      </code>
    </td>
    
    <td>
      Shortcut for <code>
        nuxt.hooks.hook
      </code>
      
      . Registers a single callback for a specific lifecycle hook.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        callHook
      </code>
    </td>
    
    <td>
      <code>
        (name: string, ...args: any[]) => Promise<any>
      </code>
    </td>
    
    <td>
      Shortcut for <code>
        nuxt.hooks.callHook
      </code>
      
      . Triggers a lifecycle hook manually and runs all registered callbacks.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        addHooks
      </code>
    </td>
    
    <td>
      <code>
        (configHooks: NestedHooks) => () => void
      </code>
    </td>
    
    <td>
      Shortcut for <code>
        nuxt.hooks.addHooks
      </code>
      
      . Registers multiple hooks at once.
    </td>
  </tr>
</tbody>
</table>

### Example

<code-group>

```ts [setupTranspilation.ts]twoslash
import { useNuxt } from '@nuxt/kit'

export const setupTranspilation = () => {
  const nuxt = useNuxt()

  if (nuxt.options.builder === '@nuxt/webpack-builder') {
    nuxt.options.build.transpile ||= []
    nuxt.options.build.transpile.push('xstate')
  }
}
```

```ts [module.ts]twoslash
// @module: esnext
// @filename: setupTranspilation.ts
export const setupTranspilation = () => {}
// @filename: module.ts
import { defineNuxtModule } from '@nuxt/kit'
// ---cut---
import { setupTranspilation } from './setupTranspilation'

export default defineNuxtModule({
  setup () {
    setupTranspilation()
  },
})
```

</code-group>

## `tryUseNuxt`

Get the Nuxt instance from the context. It will return `null` if Nuxt is not available.

### Usage

```tstwoslash
import { tryUseNuxt } from '@nuxt/kit'

function setupSomething () {
  const nuxt = tryUseNuxt()

  if (nuxt) {
    // You can now use the nuxt instance
    console.log(nuxt.options)
  } else {
    console.log('Nuxt is not available')
  }
}
```

### Type

```tstwoslash
// @errors: 2391
import type { Nuxt } from '@nuxt/schema'
// ---cut---
function tryUseNuxt (): Nuxt | null
```

### Return Value

The `tryUseNuxt` function returns the Nuxt instance if available, or `null` if Nuxt is not available.

The Nuxt instance as described in the `useNuxt` section.

### Example

<code-group>

```ts [requireSiteConfig.ts]twoslash
declare module '@nuxt/schema' {
  interface NuxtOptions {
    siteConfig: SiteConfig
  }
}
// ---cut---
import { tryUseNuxt } from '@nuxt/kit'

interface SiteConfig {
  title?: string
}

export const requireSiteConfig = (): SiteConfig => {
  const nuxt = tryUseNuxt()
  if (!nuxt) {
    return {}
  }
  return nuxt.options.siteConfig
}
```

```ts [module.ts]twoslash
// @module: esnext
// @filename: requireSiteConfig.ts
interface SiteConfig {
  title?: string
}
export const requireSiteConfig = (): SiteConfig => {
  return {}
}
// @filename: module.ts
// ---cut---
import { defineNuxtModule, useNuxt } from '@nuxt/kit'
import { requireSiteConfig } from './requireSiteConfig'

export default defineNuxtModule({
  setup (_, nuxt) {
    const config = requireSiteConfig()
    nuxt.options.app.head.title = config.title
  },
})
```

</code-group>

<style>

html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/kit/src/context.ts)
