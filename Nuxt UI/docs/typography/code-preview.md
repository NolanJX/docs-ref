---
title: "ProseCodePreview"
description: "Display code examples with a preview and their source for clearer documentation."
canonical_url: "https://ui.nuxt.com/docs/typography/code-preview"
---
# ProseCodePreview

> Display code examples with a preview and their source for clearer documentation.

## Usage

Wrap any content with the `code-preview` component to display a live preview alongside its source code using the `code` slot.

````mdc
::code-preview
`inline code`

#code
```mdc
`inline code`
```
::
````

## API

### Props

```ts
/**
 * Props for the ProseProseCodePreview component
 */
interface ProseProseCodePreviewProps {
  ui?: { root?: SlotClass; preview?: SlotClass; code?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseCodePreview component
 */
interface ProseCodePreviewSlots {
  default(): any;
  code(): any;
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseCodePreview.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-code-preview.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/CodePreview.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
