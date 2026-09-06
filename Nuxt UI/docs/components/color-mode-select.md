---
title: "ColorModeSelect"
description: "A Select to switch between system, dark and light mode."
canonical_url: "https://ui.nuxt.com/docs/components/color-mode-select"
---
# ColorModeSelect

> A Select to switch between system, dark and light mode.

## Usage

The ColorModeSelect component extends the [SelectMenu](https://ui.nuxt.com/docs/components/select-menu) component, so you can pass any property such as `color`, `variant`, `size`, etc.

```vue
<template>
  <UColorModeSelect />
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
      system: 'i-ph-desktop',
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
 * Props for the ColorModeSelect component
 */
interface ColorModeSelectProps {
  /**
   * The open state of the combobox when it is initially rendered. <br> Use when you do not need to control its open state.
   */
  defaultOpen?: boolean | undefined;
  /**
   * The controlled open state of the Combobox. Can be binded with `v-model:open`.
   */
  open?: boolean | undefined;
  /**
   * The icon displayed to open the menu.
   * @default appConfig.ui.icons.chevronDown
   */
  trailingIcon?: any;
  /**
   * @default 'primary'
   */
  color?: "primary" | "secondary" | "success" | "info" | "warning" | "error" | "neutral" | undefined;
  /**
   * Highlight the ring color like a focus state.
   */
  highlight?: boolean | undefined;
  ui?: { base?: SlotClass; leading?: SlotClass; leadingIcon?: SlotClass; leadingAvatar?: SlotClass; leadingAvatarSize?: SlotClass; trailing?: SlotClass; trailingIcon?: SlotClass; value?: SlotClass; placeholder?: SlotClass; arrow?: SlotClass; content?: SlotClass; viewport?: SlotClass; group?: SlotClass; empty?: SlotClass; label?: SlotClass; separator?: SlotClass; item?: SlotClass; itemLeadingIcon?: SlotClass; itemLeadingAvatar?: SlotClass; itemLeadingAvatarSize?: SlotClass; itemLeadingChip?: SlotClass; itemLeadingChipSize?: SlotClass; itemTrailing?: SlotClass; itemTrailingIcon?: SlotClass; itemWrapper?: SlotClass; itemLabel?: SlotClass; itemDescription?: SlotClass; input?: SlotClass; focusScope?: SlotClass; trailingClear?: SlotClass; } | undefined;
  /**
   * The name of the field. Submitted with its owning form as part of a name/value pair.
   */
  name?: string | undefined;
  /**
   * When `true`, the icon will be displayed on the right side.
   */
  trailing?: boolean | undefined;
  /**
   * The content of the menu.
   * @default { side: 'bottom', sideOffset: 8, collisionPadding: 8, position: 'popper' }
   */
  content?: Omit<ComboboxContentProps, "as" | "asChild" | "forceMount"> & Partial<EmitsToProps<DismissableLayerEmits>> | undefined;
  /**
   * When `true`, the loading icon will be displayed.
   */
  loading?: boolean | undefined;
  /**
   * @default 'md'
   */
  size?: "md" | "xs" | "sm" | "lg" | "xl" | undefined;
  /**
   * Display an avatar on the left side.
   */
  avatar?: AvatarProps | undefined;
  /**
   * @default 'outline'
   */
  variant?: "ghost" | "outline" | "soft" | "subtle" | "none" | undefined;
  autofocus?: boolean | undefined;
  /**
   * When `true`, prevents the user from interacting with listbox
   */
  disabled?: boolean | undefined;
  form?: string | undefined;
  formaction?: string | undefined;
  formenctype?: string | undefined;
  formmethod?: string | undefined;
  formnovalidate?: false | true | "true" | "false" | undefined;
  formtarget?: string | undefined;
  /**
   * When `true`, the icon will be displayed on the left side.
   */
  leading?: boolean | undefined;
  /**
   * Display an icon on the left side.
   */
  leadingIcon?: any;
  /**
   * The icon when the `loading` prop is `true`.
   * @default appConfig.ui.icons.loading
   */
  loadingIcon?: any;
  /**
   * Whether to reset the searchTerm when the Combobox input blurred
   * @default `true`
   */
  resetSearchTermOnBlur?: boolean | undefined;
  /**
   * Whether to reset the searchTerm when the Combobox value is selected
   * @default `true`
   */
  resetSearchTermOnSelect?: boolean | undefined;
  /**
   * When `true` the `modelValue` will be reset to `null` (or `[]` if `multiple`)
   */
  resetModelValueOnClear?: boolean | undefined;
  /**
   * When `true`, hover over item will trigger highlight
   */
  highlightOnHover?: boolean | undefined;
  /**
   * Use this to compare objects by a particular field, or pass your own comparison function for complete control over how objects are compared.
   */
  by?: string | (a: SelectMenuItem[], b: SelectMenuItem[]): boolean | undefined;
  /**
   * The value of the SelectMenu when initially rendered. Use when you do not need to control the state of the SelectMenu.
   */
  defaultValue?: null | string | number | bigint | false | true | { [key: string]: any; label?: string | undefined; description?: string | undefined; icon?: any; avatar?: AvatarProps | undefined; chip?: ChipProps | undefined; type?: "item" | "label" | "separator" | undefined; disabled?: boolean | undefined; onSelect?: ((e: Event) => void) | undefined; class?: any; ui?: Pick<{ base?: SlotClass; leading?: SlotClass; leadingIcon?: SlotClass; leadingAvatar?: SlotClass; leadingAvatarSize?: SlotClass; trailing?: SlotClass; trailingIcon?: SlotClass; value?: SlotClass; placeholder?: SlotClass; arrow?: SlotClass; content?: SlotClass; viewport?: SlotClass; group?: SlotClass; empty?: SlotClass; label?: SlotClass; separator?: SlotClass; item?: SlotClass; itemLeadingIcon?: SlotClass; itemLeadingAvatar?: SlotClass; itemLeadingAvatarSize?: SlotClass; itemLeadingChip?: SlotClass; itemLeadingChipSize?: SlotClass; itemTrailing?: SlotClass; itemTrailingIcon?: SlotClass; itemWrapper?: SlotClass; itemLabel?: SlotClass; itemDescription?: SlotClass; input?: SlotClass; focusScope?: SlotClass; trailingClear?: SlotClass; }, "item" | "label" | "separator" | "itemLeadingIcon" | "itemLeadingAvatar" | "itemLeadingAvatarSize" | "itemLeadingChip" | "itemLeadingChipSize" | "itemTrailing" | "itemTrailingIcon" | "itemWrapper" | "itemLabel" | "itemDescription"> | undefined; } | undefined;
  /**
   * Whether multiple options can be selected or not.
   */
  multiple?: false | undefined;
  required?: boolean | undefined;
  id?: string | undefined;
  /**
   * The placeholder text when the select is empty.
   */
  placeholder?: string | undefined;
  /**
   * Whether to display the search input or not.
   * Can be an object to pass additional props to the input.
   * `{ placeholder: 'Search...', variant: 'none' }`{lang="ts-type"}
   * Set `autofocus: false` to prevent the search input from being focused when the menu opens (e.g. to avoid opening the virtual keyboard on touch devices).
   * @default false
   */
  searchInput?: boolean | Omit<InputProps<AcceptableValue, ModelModifiers>, "modelValue" | "defaultValue"> | undefined;
  /**
   * The icon displayed when an item is selected.
   * @default appConfig.ui.icons.check
   */
  selectedIcon?: any;
  /**
   * Display a clear button to reset the model value.
   * Can be an object to pass additional props to the Button.
   * @default false
   */
  clear?: false | false & Partial<Omit<ButtonProps, LinkPropsKeys>> | undefined;
  /**
   * The icon displayed in the clear button.
   * @default appConfig.ui.icons.close
   */
  clearIcon?: any;
  /**
   * Display an arrow alongside the menu.
   * `{ rounded: true }`{lang="ts-type"}
   * @default false
   */
  arrow?: boolean | Omit<ComboboxArrowProps, "as" | "asChild"> | undefined;
  /**
   * Render the menu in a portal.
   * @default true
   */
  portal?: string | false | true | HTMLElement | undefined;
  /**
   * Enable virtualization for large lists.
   * Note: when enabled, all groups are flattened into a single list due to a limitation of Reka UI (https://github.com/unovue/reka-ui/issues/1885).
   * @default false
   */
  virtualize?: boolean | { overscan?: number | undefined; estimateSize?: number | ((index: number) => number) | undefined; } | undefined;
  /**
   * When `items` is an array of objects, select the field to use as the value instead of the object itself.
   * @default undefined
   */
  valueKey?: undefined;
  /**
   * When `items` is an array of objects, select the field to use as the label.
   * @default 'label'
   */
  labelKey?: string | undefined;
  /**
   * When `items` is an array of objects, select the field to use as the description.
   * @default 'description'
   */
  descriptionKey?: string | undefined;
  modelModifiers?: Omit<ModelModifiers, "lazy"> | undefined;
  /**
   * Keep the mobile text size on all breakpoints.
   */
  fixed?: boolean | undefined;
  /**
   * Determines if custom user input that does not exist in options can be added.
   * @default false
   */
  createItem?: boolean | "always" | { position?: "top" | "bottom" | undefined; when?: "empty" | "always" | undefined; } | undefined;
  /**
   * Fields to filter items by.
   * @default [labelKey]
   */
  filterFields?: string[] | undefined;
  /**
   * When `true`, disable the default filters, useful for custom filtering (useAsyncData, useFetch, etc.).
   * @default false
   */
  ignoreFilter?: boolean | undefined;
  autofocusDelay?: number | undefined;
}
```

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/color-mode/ColorModeSelect.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/color-mode/color-mode-select.ts).

---

- [SelectMenu](https://ui.nuxt.com/docs/components/select-menu)
- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/color-mode/ColorModeSelect.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
