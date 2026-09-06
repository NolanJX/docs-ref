---
title: "ProseKbd"
description: "Display keyboard shortcuts and key combinations with proper styling."
canonical_url: "https://ui.nuxt.com/docs/typography/kbd"
---
# ProseKbd

> Display keyboard shortcuts and key combinations with proper styling.

## Usage

Use the `kbd` component to display a [Kbd](https://ui.nuxt.com/docs/components/kbd) in your content.

```mdc
:kbd{value="meta"} :kbd{value="K"}
```

## API

### Props

```ts
/**
 * Props for the ProseProseKbd component
 */
interface ProseProseKbdProps {
  value?: string | undefined;
  ui?: { base?: any; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseKbd component
 */
interface ProseKbdSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseKbd.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-kbd.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Kbd.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
