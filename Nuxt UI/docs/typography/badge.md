---
title: "ProseBadge"
description: "Display version numbers, status labels, and tags within your content."
canonical_url: "https://ui.nuxt.com/docs/typography/badge"
---
# ProseBadge

> Display version numbers, status labels, and tags within your content.

## Usage

Use markdown in the default slot of the `badge` component to display a [Badge](https://ui.nuxt.com/docs/components/badge) in your content.

```mdc
::badge
**v4.0.0**
::
```

## API

### Props

```ts
/**
 * Props for the ProseProseBadge component
 */
interface ProseProseBadgeProps {
  ui?: { base?: any; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseBadge component
 */
interface ProseBadgeSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseBadge.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-badge.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Badge.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
