# Layers

> Nuxt Kit provides utilities to help you work with layers and their directory structures.

Nuxt layers provide a powerful way to share and extend functionality across projects. When working with layers in modules, you often need to access directory paths from each layer. Nuxt Kit provides the `getLayerDirectories` utility to access resolved directory paths for all layers in your Nuxt application.

## `getLayerDirectories`

Get the resolved directory paths for all layers in a Nuxt application. This function provides a structured way to access layer directories without directly accessing the private `nuxt.options._layers` property.

### Usage

```tstwoslash
import { defineNuxtModule, getLayerDirectories } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    const layerDirs = getLayerDirectories()

    // Access directories from all layers
    for (const [index, layer] of layerDirs.entries()) {
      console.log(`Layer ${index}:`)
      console.log(`  Root: ${layer.root}`)
      console.log(`  App: ${layer.app}`)
      console.log(`  Server: ${layer.server}`)
      console.log(`  Pages: ${layer.appPages}`)
      // ... other directories
    }
  },
})
```

### Type

```tstwoslash
// @errors: 2391
import type { Nuxt } from '@nuxt/schema'
// ---cut---
function getLayerDirectories (nuxt?: Nuxt): LayerDirectories[]

interface LayerDirectories {
  /** Nuxt rootDir (`/` by default) */
  readonly root: string
  /** Nitro source directory (`/server` by default) */
  readonly server: string
  /** Local modules directory (`/modules` by default) */
  readonly modules: string
  /** Shared directory (`/shared` by default) */
  readonly shared: string
  /** Public directory (`/public` by default) */
  readonly public: string
  /** Nuxt srcDir (`/app/` by default) */
  readonly app: string
  /** Layouts directory (`/app/layouts` by default) */
  readonly appLayouts: string
  /** Middleware directory (`/app/middleware` by default) */
  readonly appMiddleware: string
  /** Pages directory (`/app/pages` by default) */
  readonly appPages: string
  /** Plugins directory (`/app/plugins` by default) */
  readonly appPlugins: string
}
```

### Parameters

**nuxt** (optional): The Nuxt instance to get layers from. If not provided, the function will use the current Nuxt context.

### Return Value

The `getLayerDirectories` function returns an array of `LayerDirectories` objects, one for each layer in the application.

**Layer Priority Ordering**: The layers are ordered by priority, where:

- The **first layer** is the user/project layer (highest priority)
- **Earlier layers override later layers** in the array
- **Base layers appear last** in the array (lowest priority)

This ordering matches Nuxt's layer resolution system, where user-defined configurations and files take precedence over those from base layers.

**LayerDirectories**: An object containing the resolved directory paths for a layer.

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
        root
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The root directory of the layer (equivalent to <code>
        rootDir
      </code>
      
      )
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        server
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The server directory for Nitro server-side code
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        modules
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The local modules directory
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        shared
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The shared directory for code used by both client and server
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        app
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The source directory of the layer (equivalent to <code>
        srcDir
      </code>
      
      )
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        public
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The public directory for static assets
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        appLayouts
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The layouts directory for Vue layout components
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        appMiddleware
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The middleware directory for route middleware
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        appPages
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The pages directory for file-based routing
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        appPlugins
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      The plugins directory for Nuxt plugins
    </td>
  </tr>
</tbody>
</table>

### Example

**Processing files from all layers:**

```tstwoslash
// @errors: 2307
// ---cut---
import { defineNuxtModule, getLayerDirectories } from '@nuxt/kit'
import { resolve } from 'pathe'
import { globby } from 'globby'

export default defineNuxtModule({
  async setup () {
    const layerDirs = getLayerDirectories()

    // Find all component files across layers
    // Note: layerDirs[0] is the user layer (highest priority)
    // Later layers in the array have lower priority
    const componentFiles = []
    for (const [index, layer] of layerDirs.entries()) {
      const files = await globby('**/*.vue', {
        cwd: resolve(layer.app, 'components'),
        absolute: true,
      })
      console.log(`Layer ${index} (${index === 0 ? 'user' : 'base'}):`, files.length, 'components')
      componentFiles.push(...files)
    }
  },
})
```

**Adding templates from multiple layers:**

```tstwoslash
import { addTemplate, defineNuxtModule, getLayerDirectories } from '@nuxt/kit'
import { basename, resolve } from 'pathe'
import { existsSync } from 'node:fs'

export default defineNuxtModule({
  setup () {
    const layerDirs = getLayerDirectories()

    // Add a config file from each layer that has one
    for (const dirs of layerDirs) {
      const configPath = resolve(dirs.app, 'my-module.config.ts')
      if (existsSync(configPath)) {
        addTemplate({
          filename: `my-module-${basename(dirs.root)}.config.ts`,
          src: configPath,
        })
      }
    }
  },
})
```

**Respecting layer priority:**

```tstwoslash
import { defineNuxtModule, getLayerDirectories } from '@nuxt/kit'
import { resolve } from 'pathe'
import { existsSync, readFileSync } from 'node:fs'

export default defineNuxtModule({
  setup () {
    const layerDirs = getLayerDirectories()

    // Find the first (highest priority) layer that has a specific config file
    // This respects the layer priority system
    let configContent = null
    for (const dirs of layerDirs) {
      const configPath = resolve(dirs.app, 'my-config.json')
      if (existsSync(configPath)) {
        configContent = readFileSync(configPath, 'utf-8')
        console.log(`Using config from layer: ${dirs.root}`)
        break // Use the first (highest priority) config found
      }
    }

    // Alternative: Collect configs from all layers, with user layer taking precedence
    const allConfigs = {}
    for (const dirs of layerDirs.reverse()) { // Process from lowest to highest priority
      const configPath = resolve(dirs.app, 'my-config.json')
      if (existsSync(configPath)) {
        const config = JSON.parse(readFileSync(configPath, 'utf-8'))
        Object.assign(allConfigs, config) // Later assignments override earlier ones
      }
    }
  },
})
```

**Checking for layer-specific directories:**

```tstwoslash
import { defineNuxtModule, getLayerDirectories } from '@nuxt/kit'
import { existsSync } from 'node:fs'
import { resolve } from 'pathe'

export default defineNuxtModule({
  setup () {
    const layerDirs = getLayerDirectories()

    // Find layers that have a specific custom directory
    const layersWithAssets = layerDirs.filter((layer) => {
      return existsSync(resolve(layer.app, 'assets'))
    })

    console.log(`Found ${layersWithAssets.length} layers with assets directory`)
  },
})
```

<note>

The `getLayerDirectories` function includes caching via a WeakMap to avoid recomputing directory paths for the same layers repeatedly, improving performance when called multiple times.

</note>

<note>

Directory paths returned by this function always include a trailing slash for consistency.

</note>

<style>

html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sbKd-, html code.shiki .sbKd-{--shiki-light:#FF5370;--shiki-default:#FF5370;--shiki-dark:#FF9CAC}html pre.shiki code .sYRBq, html code.shiki .sYRBq{--shiki-light:#F76D47;--shiki-default:#F76D47;--shiki-dark:#F78C6C}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/kit/src/layers.ts)
