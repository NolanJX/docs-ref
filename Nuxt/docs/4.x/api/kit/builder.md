# Builder

> Nuxt Kit provides a set of utilities to help you work with the builder. These functions allow you to extend the Vite and webpack configurations.

Nuxt have builders based on [Vite](https://github.com/nuxt/nuxt/tree/main/packages/vite) and [webpack](https://github.com/nuxt/nuxt/tree/main/packages/webpack). You can extend the config passed to each one using `extendViteConfig` and `extendWebpackConfig` functions. You can also add additional plugins via `addVitePlugin`, `addWebpackPlugin` and `addBuildPlugin`.

## `extendViteConfig`

Extends the Vite configuration. Callback function can be called multiple times, when applying to both client and server builds.

<warning>

This hook is now deprecated, and we recommend using a Vite plugin instead with a `config` hook, or — for environment-specific configuration — the `applyToEnvironment` hook.

</warning>

### Usage

```tstwoslash
import { defineNuxtModule, extendViteConfig } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    extendViteConfig((config) => {
      config.optimizeDeps ||= {}
      config.optimizeDeps.include ||= []
      config.optimizeDeps.include.push('cross-fetch')
    })
  },
})
```

For environment-specific configuration in Nuxt 5+, use `addVitePlugin()` instead:

```tstwoslash
import { addVitePlugin, defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    // For global configuration (affects all environments)
    addVitePlugin(() => ({
      name: 'my-global-plugin',
      config (config) {
        // This runs before environment setup
        config.optimizeDeps ||= {}
        config.optimizeDeps.include ||= []
        config.optimizeDeps.include.push('cross-fetch')
      },
    }))

    // For environment-specific configuration
    addVitePlugin(() => ({
      name: 'my-client-plugin',
      applyToEnvironment (environment) {
        return environment.name === 'client'
      },
      configEnvironment (name, config) {
        // This only affects the client environment
        config.optimizeDeps ||= {}
        config.optimizeDeps.include ||= []
        config.optimizeDeps.include.push('client-only-package')
      },
    }))
  },
})
```

<warning>

**Important:** The `config` hook runs before `applyToEnvironment` and modifies the global configuration. Use `configEnvironment` for environment-specific configuration changes.

</warning>

### Type

```tstwoslash
// @errors: 2391
import type { UserConfig as ViteConfig } from 'vite'
import type { ExtendViteConfigOptions } from '@nuxt/kit'
// ---cut---
function extendViteConfig (callback: ((config: ViteConfig) => void), options?: ExtendViteConfigOptions): void
```

<read-more to="https://vite.dev/config/" icon="i-simple-icons-vite" target="_blank">

Check out the Vite website for more information about its configuration.

</read-more>

### Parameters

**callback**: A callback function that will be called with the Vite configuration object.

**options**: Options to pass to the callback function. This object can have the following properties:

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
        dev
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in development mode.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        build
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in production mode.
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
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the server bundle. <strong>
        Deprecated in Nuxt 5+.
      </strong>
      
       Use <code>
        addVitePlugin()
      </code>
      
       with <code>
        applyToEnvironment()
      </code>
      
       instead.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        client
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the client bundle. <strong>
        Deprecated in Nuxt 5+.
      </strong>
      
       Use <code>
        addVitePlugin()
      </code>
      
       with <code>
        applyToEnvironment()
      </code>
      
       instead.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        prepend
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
      If set to <code>
        true
      </code>
      
      , the callback function will be prepended to the array with <code>
        unshift()
      </code>
      
       instead of <code>
        push()
      </code>
      
      .
    </td>
  </tr>
</tbody>
</table>

## `extendWebpackConfig`

Extends the webpack configuration. Callback function can be called multiple times, when applying to both client and server builds.

### Usage

```tstwoslash
import { defineNuxtModule, extendWebpackConfig } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    extendWebpackConfig((config) => {
      config.module!.rules!.push({
        test: /\.txt$/,
        use: 'raw-loader',
      })
    })
  },
})
```

### Type

```tstwoslash
// @errors: 2391
import type { Configuration as WebpackConfig } from 'webpack'
import type { ExtendWebpackConfigOptions } from '@nuxt/kit'
// ---cut---
function extendWebpackConfig (callback: ((config: WebpackConfig) => void), options?: ExtendWebpackConfigOptions): void
```

<read-more to="https://webpack.js.org/configuration/" icon="i-simple-icons-webpack" target="_blank">

Check out webpack website for more information about its configuration.

</read-more>

### Parameters

**callback**: A callback function that will be called with the webpack configuration object.

**options**: Options to pass to the callback function. This object can have the following properties:

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
        dev
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in development mode.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        build
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in production mode.
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
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the server bundle.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        client
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the client bundle.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        prepend
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
      If set to <code>
        true
      </code>
      
      , the callback function will be prepended to the array with <code>
        unshift()
      </code>
      
       instead of <code>
        push()
      </code>
      
      .
    </td>
  </tr>
</tbody>
</table>

## `addVitePlugin`

Append Vite plugin to the config.

<warning>

In Nuxt 5+, plugins registered with `server: false` or `client: false` options will not have their `config` or `configResolved` hooks called. Instead, use the `applyToEnvironment()` method instead for environment-specific plugins.

</warning>

### Usage

```tstwoslash
// @errors: 2307
// ---cut---
import { addVitePlugin, defineNuxtModule } from '@nuxt/kit'
import { svg4VuePlugin } from 'vite-plugin-svg4vue'

export default defineNuxtModule({
  meta: {
    name: 'nuxt-svg-icons',
    configKey: 'nuxtSvgIcons',
  },
  defaults: {
    svg4vue: {
      assetsDirName: 'assets/icons',
    },
  },
  setup (options) {
    addVitePlugin(svg4VuePlugin(options.svg4vue))

    // or, to add a vite plugin to only one environment
    addVitePlugin(() => ({
      name: 'my-client-plugin',
      applyToEnvironment (environment) {
        return environment.name === 'client'
      },
      // ... rest of your client-only plugin
    }))
  },
})
```

### Type

```tstwoslash
// @errors: 2391
import type { Plugin as VitePlugin } from 'vite'
import type { ExtendViteConfigOptions } from '@nuxt/kit'
// ---cut---
function addVitePlugin (pluginOrGetter: VitePlugin | VitePlugin[] | (() => VitePlugin | VitePlugin[]), options?: ExtendViteConfigOptions): void
```

<tip>

See [Vite website](https://vite.dev/guide/api-plugin) for more information about Vite plugins. You can also use [this repository](https://github.com/vitejs/awesome-vite#plugins) to find a plugin that suits your needs.

</tip>

### Parameters

**pluginOrGetter**: A Vite plugin instance or an array of Vite plugin instances. If a function is provided, it must return a Vite plugin instance or an array of Vite plugin instances. The function can also be async or return a Promise, which is useful for lazy-loading plugins:

```tstwoslash
// @errors: 2307
import { addVitePlugin, defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    // Lazy load the plugin - only imported when the build actually runs
    addVitePlugin(() => import('my-vite-plugin').then(r => r.default()))
  },
})
```

**options**: Options to pass to the callback function. This object can have the following properties:

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
        dev
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in development mode.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        build
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in production mode.
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
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the server bundle. <strong>
        Deprecated in Nuxt 5+.
      </strong>
      
       Use <code>
        applyToEnvironment()
      </code>
      
       instead.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        client
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the client bundle. <strong>
        Deprecated in Nuxt 5+.
      </strong>
      
       Use <code>
        applyToEnvironment()
      </code>
      
       instead.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        prepend
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
      If set to <code>
        true
      </code>
      
      , the callback function will be prepended to the array with <code>
        unshift()
      </code>
      
       instead of <code>
        push()
      </code>
      
      .
    </td>
  </tr>
</tbody>
</table>

## `addWebpackPlugin`

Append webpack plugin to the config.

### Usage

```ts
import EslintWebpackPlugin from 'eslint-webpack-plugin'
import { addWebpackPlugin, defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  meta: {
    name: 'nuxt-eslint',
    configKey: 'eslint',
  },
  defaults: nuxt => ({
    include: [`${nuxt.options.srcDir}/**/*.{js,jsx,ts,tsx,vue}`],
    lintOnStart: true,
  }),
  setup (options, nuxt) {
    const webpackOptions = {
      ...options,
      context: nuxt.options.srcDir,
      files: options.include,
      lintDirtyModulesOnly: !options.lintOnStart,
    }
    addWebpackPlugin(new EslintWebpackPlugin(webpackOptions), { server: false })
  },
})
```

### Type

```tstwoslash
// @errors: 2391
import type { WebpackPluginInstance } from 'webpack'
import type { ExtendWebpackConfigOptions } from '@nuxt/kit'
// ---cut---
function addWebpackPlugin (pluginOrGetter: WebpackPluginInstance | WebpackPluginInstance[] | (() => WebpackPluginInstance | WebpackPluginInstance[]), options?: ExtendWebpackConfigOptions): void
```

<tip>

See [webpack website](https://webpack.js.org/concepts/plugins/) for more information about webpack plugins. You can also use [this collection](https://webpack.js.org/awesome-webpack/#webpack-plugins) to find a plugin that suits your needs.

</tip>

### Parameters

**pluginOrGetter**: A webpack plugin instance or an array of webpack plugin instances. If a function is provided, it must return a webpack plugin instance or an array of webpack plugin instances. The function can also be async or return a Promise, enabling lazy-loading of plugins.

**options**: Options to pass to the callback function. This object can have the following properties:

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
        dev
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in development mode.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        build
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in production mode.
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
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the server bundle.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        client
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the client bundle.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        prepend
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
      If set to <code>
        true
      </code>
      
      , the callback function will be prepended to the array with <code>
        unshift()
      </code>
      
       instead of <code>
        push()
      </code>
      
      .
    </td>
  </tr>
</tbody>
</table>

## `addBuildPlugin`

Builder-agnostic version of `addVitePlugin` and `addWebpackPlugin`. It will add the plugin to both Vite and webpack configurations if they are present.

### Type

```tstwoslash
// @errors: 2391
import type { ExtendConfigOptions } from '@nuxt/kit'
import type { Plugin as VitePlugin } from 'vite'
import type { WebpackPluginInstance } from 'webpack'
import type { RspackPluginInstance } from '@rspack/core'

interface AddBuildPluginFactory {
  vite?: () => VitePlugin | VitePlugin[]
  webpack?: () => WebpackPluginInstance | WebpackPluginInstance[]
  rspack?: () => RspackPluginInstance | RspackPluginInstance[]
}
// ---cut---
function addBuildPlugin (pluginFactory: AddBuildPluginFactory, options?: ExtendConfigOptions): void
```

### Parameters

**pluginFactory**: A factory function that returns an object with `vite` and/or `webpack` properties. These properties must be functions that return a Vite plugin instance or an array of Vite plugin instances and/or a webpack plugin instance or an array of webpack plugin instances.

**options**: Options to pass to the callback function. This object can have the following properties:

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
        dev
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in development mode.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        build
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building in production mode.
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
        boolean
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the server bundle.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        client
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
      If set to <code>
        true
      </code>
      
      , the callback function will be called when building the client bundle.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        prepend
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
      If set to <code>
        true
      </code>
      
      , the callback function will be prepended to the array with <code>
        unshift()
      </code>
      
       instead of <code>
        push()
      </code>
      
      .
    </td>
  </tr>
</tbody>
</table>

## `setBuildOutput`

Set a build output provider for the given key. Build outputs are the contract between builders (Vite, webpack, Rspack, or a custom builder) and the Nitro server runtime: each key maps to a `nuxt/*` subpath import the server runtime resolves at build time. This is typically called from inside a builder, or from a module that participates in the build.

<read-more to="/docs/4.x/guide/going-further/builders#the-build-output-contract">

Learn how the build output contract works.

</read-more>

### Type

```tstwoslash
// @errors: 2391
import type { NuxtBuildOutputs } from '@nuxt/schema'
// ---cut---
function setBuildOutput<K extends keyof NuxtBuildOutputs> (key: K, provider: NuxtBuildOutputs[K]): void
```

### Parameters

**key**: The build output key. One of `serverEntry`, `clientManifest`, `clientPrecomputed`, `ssrStyles`, `entryChunkName` or `entryIds`.

**provider**: The value for that key: a (possibly async) function returning the module body as a string, read lazily when the server build resolves the corresponding `nuxt/*` import.

### Example

```ts
import { setBuildOutput } from '@nuxt/kit'

// Re-export the built SSR entry by absolute specifier.
setBuildOutput('serverEntry', () => `export { default } from ${JSON.stringify(serverEntryURL)}`)

// Provide the serialized client manifest.
setBuildOutput('clientManifest', () => `export default ${serializedManifest}`)

// Re-export the emitted per-component styles map for `nuxt/styles`.
setBuildOutput('ssrStyles', () => `export { default } from ${JSON.stringify(pathToFileURL(resolve(serverDir, 'styles.mjs')).href)}`)
```

<style>

html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sbKd-, html code.shiki .sbKd-{--shiki-light:#FF5370;--shiki-default:#FF5370;--shiki-dark:#FF9CAC}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/kit/src/build.ts)
