# Resolving

> Nuxt Kit provides utilities to resolve paths from the Nuxt root directory or a custom base.

Sometimes you need to resolve a path without knowing its name or extension. For example, you may want to add a plugin that is located in the same directory as a module. To handle these cases, Nuxt provides a set of utilities to resolve paths. `resolvePath` resolves paths from the Nuxt root directory by default, while `resolveAlias` applies configured aliases. `findPath` finds the first existing file in a given set of paths. `createResolver` creates a resolver relative to a base path.

## `resolvePath`

Resolves the full path to a file or directory, respecting Nuxt alias and extensions options. If a path could not be resolved, a normalized input path will be returned.

### Usage

```ts
import { defineNuxtModule, resolvePath } from '@nuxt/kit'

export default defineNuxtModule({
  async setup () {
    const entrypoint = await resolvePath('@unhead/vue')
    console.log(`Unhead entrypoint is ${entrypoint}`)
  },
})
```

### Type

```ts
function resolvePath (path: string, options?: ResolvePathOptions): Promise<string>
```

### Parameters

**path**: A path to resolve.

**options**: Options to pass to the resolver. This object can have the following properties:

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
      Required
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
        cwd
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Base for resolving paths from. Default is Nuxt rootDir.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        alias
      </code>
    </td>
    
    <td>
      <code className="language-ts shiki shiki-themes material-theme-lighter material-theme-lighter material-theme-palenight" language="ts" style="">
        <span class="sZSNi">
          Record
        </span>
        
        <span class="sDfIl">
          <
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          ,
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          >
        </span>
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      An object of aliases. Default is Nuxt configured aliases.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        extensions
      </code>
    </td>
    
    <td>
      <code>
        string[]
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      The file extensions to try. Default is Nuxt configured extensions.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        virtual
      </code>
    </td>
    
    <td>
      <code>
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Whether to resolve files that exist in the Nuxt VFS (for example, as a Nuxt template).
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        fallbackToOriginal
      </code>
    </td>
    
    <td>
      <code>
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Whether to fallback to the original path if the resolved path does not exist instead of returning the normalized input path.
    </td>
  </tr>
</tbody>
</table>

<note>

`resolvePath` follows standard module resolution and does not search dependencies nested inside other packages. To resolve a dependency declared by your Nuxt module, use `createResolver(import.meta.url).resolvePath()`. This makes resolution independent of whether the package manager hoists the dependency.

</note>

### Example

```ts
import { defineNuxtModule, resolvePath } from '@nuxt/kit'
import { join } from 'pathe'

const headlessComponents: ComponentGroup[] = [
  {
    relativePath: 'combobox/combobox.js',
    chunkName: 'headlessui/combobox',
    exports: [
      'Combobox',
      'ComboboxLabel',
      'ComboboxButton',
      'ComboboxInput',
      'ComboboxOptions',
      'ComboboxOption',
    ],
  },
]

export default defineNuxtModule({
  meta: {
    name: 'nuxt-headlessui',
    configKey: 'headlessui',
  },
  defaults: {
    prefix: 'Headless',
  },
  async setup (options) {
    const entrypoint = await resolvePath('@headlessui/vue')
    const root = join(entrypoint, '../components')

    for (const group of headlessComponents) {
      for (const e of group.exports) {
        addComponent(
          {
            name: e,
            export: e,
            filePath: join(root, group.relativePath),
            chunkName: group.chunkName,
            mode: 'all',
          },
        )
      }
    }
  },
})
```

## `resolveAlias`

Resolves path aliases respecting Nuxt alias options.

### Type

```ts
function resolveAlias (path: string, alias?: Record<string, string>): string
```

### Parameters

**path**: A path to resolve.

**alias**: An object of aliases. If not provided, it will be read from `nuxt.options.alias`.

## `findPath`

Try to resolve first existing file in a given set of paths.

### Usage

```ts
import { defineNuxtModule, findPath } from '@nuxt/kit'
import { join } from 'pathe'

export default defineNuxtModule({
  async setup (_, nuxt) {
    // Resolve main (app.vue)
    const mainComponent = await findPath([
      join(nuxt.options.srcDir, 'App'),
      join(nuxt.options.srcDir, 'app'),
    ])
  },
})
```

### Type

```ts
function findPath (paths: string | string[], options?: ResolvePathOptions, pathType: 'file' | 'dir'): Promise<string | null>
```

### Parameters

**paths**: A path or an array of paths to resolve.

**options**: Options to pass to the resolver. This object can have the following properties:

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
      Required
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
        cwd
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Base for resolving paths from. Default is Nuxt rootDir.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        alias
      </code>
    </td>
    
    <td>
      <code className="language-ts shiki shiki-themes material-theme-lighter material-theme-lighter material-theme-palenight" language="ts" style="">
        <span class="sZSNi">
          Record
        </span>
        
        <span class="sDfIl">
          <
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          ,
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          >
        </span>
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      An object of aliases. Default is Nuxt configured aliases.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        extensions
      </code>
    </td>
    
    <td>
      <code>
        string[]
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      The file extensions to try. Default is Nuxt configured extensions.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        virtual
      </code>
    </td>
    
    <td>
      <code>
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Whether to resolve files that exist in the Nuxt VFS (for example, as a Nuxt template).
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        fallbackToOriginal
      </code>
    </td>
    
    <td>
      <code>
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Whether to fallback to the original path if the resolved path does not exist instead of returning the normalized input path.
    </td>
  </tr>
</tbody>
</table>

## `createResolver`

Creates resolver relative to base path.

<tip icon="i-lucide-video" target="_blank" to="https://vueschool.io/lessons/resolving-paths-and-injecting-assets-to-the-app?friend=nuxt">

Watch Vue School video about createResolver.

</tip>

### Usage

```ts
import { createResolver, defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  setup (_, nuxt) {
    const { resolve, resolvePath } = createResolver(import.meta.url)
  },
})
```

### Type

```ts
function createResolver (basePath: string | URL): Resolver
```

### Parameters

**basePath**: A base path to resolve from. It can be a string or a URL.

### Return Value

The `createResolver` function returns an object with the following properties:

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
        resolve
      </code>
    </td>
    
    <td>
      <code className="language-ts shiki shiki-themes material-theme-lighter material-theme-lighter material-theme-palenight" language="ts" style="">
        <span class="sDfIl">
          (
        </span>
        
        <span class="s1nJG">
          path
        </span>
        
        <span class="sDfIl">
          :
        </span>
        
        <span class="s52Pk">
          string
        </span>
        
        <span class="sDfIl">
          )
        </span>
        
        <span class="smZ93">
          =>
        </span>
        
        <span class="sZSNi">
          string
        </span>
      </code>
    </td>
    
    <td>
      A function that resolves a path relative to the base path.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        resolvePath
      </code>
    </td>
    
    <td>
      <code className="language-ts shiki shiki-themes material-theme-lighter material-theme-lighter material-theme-palenight" language="ts" style="">
        <span class="sDfIl">
          (
        </span>
        
        <span class="s1nJG">
          path
        </span>
        
        <span class="sDfIl">
          :
        </span>
        
        <span class="s52Pk">
          string
        </span>
        
        <span class="sDfIl">
          ,
        </span>
        
        <span class="s1nJG">
          options
        </span>
        
        <span class="sDfIl">
          ?:
        </span>
        
        <span class="s52Pk">
          ResolvePathOptions
        </span>
        
        <span class="sDfIl">
          )
        </span>
        
        <span class="smZ93">
          =>
        </span>
        
        <span class="s52Pk">
          Promise
        </span>
        
        <span class="sDfIl">
          <
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          >
        </span>
      </code>
    </td>
    
    <td>
      A function that resolves a path relative to the base path and respects Nuxt alias and extensions options.
    </td>
  </tr>
</tbody>
</table>

### Example

```ts
import { createResolver, defineNuxtModule, isNuxt2 } from '@nuxt/kit'

export default defineNuxtModule({
  setup (options, nuxt) {
    const resolver = createResolver(import.meta.url)

    nuxt.hook('modules:done', () => {
      if (isNuxt2()) {
        addPlugin(resolver.resolve('./runtime/plugin.vue2'))
      } else {
        addPlugin(resolver.resolve('./runtime/plugin.vue3'))
      }
    })
  },
})
```

<style>

html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/kit/src/resolve.ts)
