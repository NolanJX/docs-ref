# reloadNuxtApp

> reloadNuxtApp will perform a hard reload of the page.

<note>

`reloadNuxtApp` will perform a hard reload of your app, re-requesting a page and its dependencies from the server.

</note>

By default, it will also save the current `state` of your app (that is, any state you could access with `useState`).

<read-more icon="i-lucide-star" to="/docs/4.x/guide/going-further/experimental-features#restorestate">

You can enable experimental restoration of this state by enabling the `experimental.restoreState` option in your `nuxt.config` file.

</read-more>

## Type

```ts [Signature]
export function reloadNuxtApp (options?: ReloadNuxtAppOptions)

interface ReloadNuxtAppOptions {
  ttl?: number
  force?: boolean
  path?: string
  persistState?: boolean
}
```

### `options` (optional)

**Type**: `ReloadNuxtAppOptions`

An object accepting the following properties:

- `path` (optional)<br />

**Type**: `string`<br />

**Default**: `window.location.pathname`<br />

The path to reload (defaulting to the current path). If this is different from the current window location it
will trigger a navigation and add an entry in the browser history.
- `ttl` (optional)<br />

**Type**: `number`<br />

**Default**: `10000`<br />

The number of milliseconds in which to ignore future reload requests. If called again within this time period,
`reloadNuxtApp` will not reload your app to avoid reload loops.
- `force` (optional)<br />

**Type**: `boolean`<br />

**Default**: `false`<br />

This option allows bypassing reload loop protection entirely, forcing a reload even if one has occurred within
the previously specified TTL.
- `persistState` (optional)<br />

**Type**: `boolean`<br />

**Default**: `false`<br />

Whether to dump the current Nuxt state to sessionStorage (as `nuxt:reload:state`). By default this will have no
effect on reload unless `experimental.restoreState` is also set, or unless you handle restoring the state yourself.

<style>

html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/nuxt/src/app/composables/chunk.ts)
