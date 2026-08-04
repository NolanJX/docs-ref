# <NuxtClientFallback>

> Nuxt provides the <NuxtClientFallback> component to render its content on the client if any of its children trigger an error in SSR

Nuxt provides the `<NuxtClientFallback>` component to render its content on the client if any of its children trigger an error in SSR.

<note to="/docs/4.x/guide/going-further/experimental-features#clientfallback">

This component is experimental and in order to use it you must enable the `experimental.clientFallback` option in your `nuxt.config`.

</note>

```vue [app/pages/example.vue]
<template>
  <div>
    <Sidebar />
    <!-- this component will be rendered on client-side -->
    <NuxtClientFallback fallback-tag="span">
      <Comments />
      <BrokeInSSR />
    </NuxtClientFallback>
  </div>
</template>
```

## Events

- `@ssr-error`: Event emitted when a child triggers an error in SSR. Note that this will only be triggered on the server.```vue
<template>
  <NuxtClientFallback @ssr-error="logSomeError">
    <!-- ... -->
  </NuxtClientFallback>
</template>
```

## Props

- `placeholderTag` | `fallbackTag`: Specify a fallback tag to be rendered if the slot fails to render on the server.

  - **type**: `string`
  - **default**: `div`
- `placeholder` | `fallback`: Specify fallback content to be rendered if the slot fails to render.

  - **type**: `string`
- `keepFallback`: Keep the fallback content if it failed to render server-side.

  - **type**: `boolean`
  - **default**: `false`

<warning icon="i-ph-warning-duotone">

The `placeholder` and `fallback` props render content as raw HTML. Do not pass untrusted user input to these props as it may lead to XSS vulnerabilities. Use the `#fallback` or `#placeholder` slots instead for dynamic content that needs proper escaping.

</warning>

```vue
<template>
  <!-- render <span>Hello world</span> server-side if the default slot fails to render -->
  <NuxtClientFallback
    fallback-tag="span"
    fallback="Hello world"
  >
    <BrokeInSSR />
  </NuxtClientFallback>
</template>
```

## Slots

- `#fallback`: specify content to be displayed server-side if the slot fails to render.

```vue
<template>
  <NuxtClientFallback>
    <!-- ... -->
    <template #fallback>
      <!-- this will be rendered on server side if the default slot fails to render in ssr -->
      <p>Hello world</p>
    </template>
  </NuxtClientFallback>
</template>
```

<style>

html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}

</style>

---

- [Source (client)](https://github.com/nuxt/nuxt/blob/main/packages/nuxt/src/app/components/client-fallback.client.ts)
- [Source (server)](https://github.com/nuxt/nuxt/blob/main/packages/nuxt/src/app/components/client-fallback.server.ts)
