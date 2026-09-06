---
title: "ColorModeSwitch"
description: "A Switch to toggle between light and dark mode."
canonical_url: "https://ui.nuxt.com/docs/components/color-mode-switch"
---
# ColorModeSwitch

> A Switch to toggle between light and dark mode.

## Usage

The ColorModeSwitch component extends the [Switch](https://ui.nuxt.com/docs/components/switch) component, so you can pass any property such as `color`, `size`, etc.

```vue
<template>
  <UColorModeSwitch />
</template>
```

## Examples

### With custom icons

**Nuxt:**

Use the `app.config.ts` to customize the icon with the `ui.icons` property:

```ts [app/app.config.ts]
export default defineAppConfig({
  ui: {
    icons: {
      light: 'i-ph-sun',
      dark: 'i-ph-moon'
    }
  }
})
```

**Vue:**

Use the `vite.config.ts` to customize the icon with the `ui.icons` property:

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      ui: {
        icons: {
          light: 'i-ph-sun',
          dark: 'i-ph-moon'
        }
      }
    })
  ]
})
```

## API

### Props

```ts
/**
 * Props for the ColorModeSwitch component
 */
interface ColorModeSwitchProps {
  /**
   * The element or component this component should render as.
   * @default 'div'
   */
  as?: any;
  /**
   * @default 'primary'
   */
  color?: "primary" | "secondary" | "success" | "info" | "warning" | "error" | "neutral" | undefined;
  /**
   * Highlight the ring color like a focus state.
   */
  highlight?: boolean | undefined;
  ui?: { root?: SlotClass; base?: SlotClass; container?: SlotClass; thumb?: SlotClass; icon?: SlotClass; wrapper?: SlotClass; label?: SlotClass; description?: SlotClass; } | undefined;
  /**
   * The name of the field. Submitted with its owning form as part of a name/value pair.
   */
  name?: string | undefined;
  /**
   * When `true`, the loading icon will be displayed.
   */
  loading?: boolean | undefined;
  /**
   * @default 'md'
   */
  size?: "md" | "xs" | "sm" | "lg" | "xl" | undefined;
  autofocus?: false | true | "true" | "false" | undefined;
  /**
   * When `true`, prevents the user from interacting with the switch.
   */
  disabled?: boolean | undefined;
  form?: string | undefined;
  formaction?: string | undefined;
  formenctype?: string | undefined;
  formmethod?: string | undefined;
  formnovalidate?: false | true | "true" | "false" | undefined;
  formtarget?: string | undefined;
  label?: string | undefined;
  /**
   * The icon when the `loading` prop is `true`.
   * @default appConfig.ui.icons.loading
   */
  loadingIcon?: any;
  /**
   * The state of the switch when it is initially rendered. Use when you do not need to control its state.
   */
  defaultValue?: boolean | undefined;
  /**
   * When `true`, indicates that the user must set the value before the owning form can be submitted.
   */
  required?: boolean | undefined;
  id?: string | undefined;
  /**
   * The value given as data when submitted with a `name`.
   */
  value?: string | undefined;
  description?: string | undefined;
  /**
   * The value used when the switch is on. Defaults to `true`.
   */
  trueValue?: boolean | undefined;
  /**
   * The value used when the switch is off. Defaults to `false`.
   */
  falseValue?: boolean | undefined;
}
```

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/color-mode/ColorModeSwitch.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/color-mode/color-mode-switch.ts).

---

- [Switch](https://ui.nuxt.com/docs/components/switch)
- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/color-mode/ColorModeSwitch.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
