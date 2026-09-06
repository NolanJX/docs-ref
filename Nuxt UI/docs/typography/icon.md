---
title: "ProseIcon"
description: "Display icons from popular icon libraries to enhance your content."
canonical_url: "https://ui.nuxt.com/docs/typography/icon"
---
# ProseIcon

> Display icons from popular icon libraries to enhance your content.

## Usage

Use the `icon` component to display an [Icon](https://ui.nuxt.com/docs/components/icon) in your content.

```mdc
:icon{name="i-simple-icons-nuxtdotjs"}
```

## API

### Props

```ts
/**
 * Props for the ProseProseIcon component
 */
interface ProseProseIconProps {
  name: string;
  ui?: { base?: any; } | undefined;
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseIcon.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-icon.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Icon.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
