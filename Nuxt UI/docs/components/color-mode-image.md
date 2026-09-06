---
title: "ColorModeImage"
description: "An image element with a different source for light and dark mode."
canonical_url: "https://ui.nuxt.com/docs/components/color-mode-image"
---
# ColorModeImage

> An image element with a different source for light and dark mode.

## Usage

The ColorModeImage component uses the `<NuxtImg>` component when [`@nuxt/image`](https://github.com/nuxt/image) is installed, falling back to `img` otherwise.

```vue
<template>
  <UColorModeImage light="https://picsum.photos/id/29/400" dark="https://picsum.photos/id/46/400" :width="200" :height="200" />
</template>
```

> [!NOTE]
> 
> Switch between light and dark mode to see the different images:

## API

### Props

```ts
/**
 * Props for the ColorModeImage component
 */
interface ColorModeImageProps {
  dark: string;
  light: string;
  alt?: string | undefined;
  decoding?: "async" | "auto" | "sync" | undefined;
  height?: string | number | undefined;
  loading?: "lazy" | "eager" | undefined;
  referrerpolicy?: "" | "no-referrer" | "no-referrer-when-downgrade" | "origin" | "origin-when-cross-origin" | "same-origin" | "strict-origin" | "strict-origin-when-cross-origin" | "unsafe-url" | undefined;
  sizes?: string | undefined;
  srcset?: string | undefined;
  usemap?: string | undefined;
  width?: string | number | undefined;
  crossorigin?: "anonymous" | "use-credentials" | undefined;
}
```

> [!NOTE]
> See: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attributes
> 
> This component also supports all native `<img>` HTML attributes.

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/color-mode/ColorModeImage.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/color-mode/color-mode-image.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/color-mode/ColorModeImage.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
