---
title: "ColorModeAvatar"
description: "An Avatar with a different source for light and dark mode."
canonical_url: "https://ui.nuxt.com/docs/components/color-mode-avatar"
---
# ColorModeAvatar

> An Avatar with a different source for light and dark mode.

## Usage

The ColorModeAvatar component extends the [Avatar](https://ui.nuxt.com/docs/components/avatar) component, so you can pass any property such as `size`, `icon`, etc.

Use the `light` and `dark` props to define the source for light and dark mode.

```vue
<template>
  <UColorModeAvatar light="https://github.com/vuejs.png" dark="https://github.com/nuxt.png" />
</template>
```

> [!NOTE]
> 
> Switch between light and dark mode to see the different images:

## API

### Props

```ts
/**
 * Props for the ColorModeAvatar component
 */
interface ColorModeAvatarProps {
  light: string;
  dark: string;
  /**
   * The element or component this component should render as.
   * @default 'span'
   */
  as?: any;
  /**
   * @default 'neutral'
   */
  color?: "primary" | "secondary" | "success" | "info" | "warning" | "error" | "neutral" | undefined;
  ui?: { root?: SlotClass; image?: SlotClass; fallback?: SlotClass; icon?: SlotClass; } | undefined;
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
  icon?: any;
  text?: string | undefined;
  /**
   * @default 'md'
   */
  size?: "md" | "3xs" | "2xs" | "xs" | "sm" | "lg" | "xl" | "2xl" | "3xl" | undefined;
  chip?: boolean | ChipProps | undefined;
}
```

> [!NOTE]
> See: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attributes
> 
> This component also supports all native `<img>` HTML attributes.

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/color-mode/ColorModeAvatar.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/color-mode/color-mode-avatar.ts).

---

- [Avatar](https://ui.nuxt.com/docs/components/avatar)
- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/color-mode/ColorModeAvatar.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
