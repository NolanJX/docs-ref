---
title: "ContentSearchButton"
description: "A pre-styled Button to open the ContentSearch modal."
canonical_url: "https://ui.nuxt.com/docs/components/content-search-button"
---
# ContentSearchButton

> A pre-styled Button to open the ContentSearch modal.

> [!WARNING]
> See: /docs/getting-started/integrations/content
> 
> This component is only available when the `@nuxt/content` module is installed.

## Usage

The ContentSearchButton component is used to open the [ContentSearch](https://ui.nuxt.com/docs/components/content-search) modal.

```vue
<template>
  <UContentSearchButton />
</template>
```

It extends the [Button](https://ui.nuxt.com/docs/components/button) component, so you can pass any property such as `color`, `variant`, `size`, etc.

```vue
<template>
  <UContentSearchButton variant="subtle" />
</template>
```

> [!NOTE]
> See: #collapsed
> 
> The button defaults to `color="neutral"` and `variant="outline"` when not collapsed, `variant="ghost"` when collapsed.

### Collapsed

Use the `collapsed` prop to show the button's label and [kbds](#kbds). Defaults to `true`.

```vue
<template>
  <UContentSearchButton :collapsed="false" />
</template>
```

### Kbds

Use the `kbds` prop to display keyboard keys in the button. Defaults to `['meta', 'K']` to match the default shortcut of the [ContentSearch](https://ui.nuxt.com/docs/components/content-search#shortcut) component.

```vue
<template>
  <UContentSearchButton :collapsed="false" :kbds="[
  'alt',
  'O'
]" />
</template>
```

## API

### Props

```ts
/**
 * Props for the ContentSearchButton component
 */
interface ContentSearchButtonProps {
  /**
   * The icon displayed in the button. Set to `false` to hide the icon.
   * @default appConfig.ui.icons.search
   */
  icon?: any;
  /**
   * The label displayed in the button.
   * @default t('contentSearchButton.label')
   */
  label?: string | undefined;
  /**
   * The color of the button.
   * @default 'neutral'
   */
  color?: "error" | "neutral" | "primary" | "secondary" | "success" | "info" | "warning" | undefined;
  /**
   * The variant of the button.
   * Defaults to 'outline' when not collapsed, 'ghost' when collapsed.
   */
  variant?: "solid" | "outline" | "soft" | "subtle" | "ghost" | "link" | undefined;
  /**
   * Whether the button is collapsed.
   * @default true
   */
  collapsed?: boolean | undefined;
  /**
   * Display a tooltip on the button when is collapsed with the button label.
   * This has priority over the global `tooltip` prop.
   * @default false
   */
  tooltip?: boolean | TooltipProps | undefined;
  /**
   * The keyboard keys to display in the button.
   * `{ variant: 'subtle' }`{lang="ts-type"}
   * @default ["meta", "k"]
   */
  kbds?: (string | undefined)[] | KbdProps[] | undefined;
  ui?: { base?: SlotClass; label?: SlotClass; trailing?: SlotClass; } & { base?: SlotClass; label?: SlotClass; leadingIcon?: SlotClass; leadingAvatar?: SlotClass; leadingAvatarSize?: SlotClass; trailingIcon?: SlotClass; } | undefined;
  /**
   * Class to apply when the link is exact active
   */
  exactActiveClass?: string | undefined;
  /**
   * Pass the returned promise of `router.push()` to `document.startViewTransition()` if supported.
   */
  viewTransition?: boolean | undefined;
  autofocus?: false | true | "true" | "false" | undefined;
  disabled?: boolean | undefined;
  form?: string | undefined;
  formaction?: string | undefined;
  formenctype?: string | undefined;
  formmethod?: string | undefined;
  formnovalidate?: false | true | "true" | "false" | undefined;
  formtarget?: string | undefined;
  name?: string | undefined;
  /**
   * The type of the button when not a link.
   * @default 'button'
   */
  type?: "reset" | "submit" | "button" | undefined;
  onClick?: (event: MouseEvent): void | ((event: MouseEvent) => void)[] | undefined;
  /**
   * The element or component this component should render as when not a link.
   * @default 'button'
   */
  as?: any;
  activeColor?: "error" | "neutral" | "primary" | "secondary" | "success" | "info" | "warning" | undefined;
  activeVariant?: "solid" | "outline" | "soft" | "subtle" | "ghost" | "link" | undefined;
  /**
   * @default 'md'
   */
  size?: "xs" | "sm" | "md" | "lg" | "xl" | undefined;
  /**
   * Render the button with equal padding on all sides.
   */
  square?: boolean | undefined;
  /**
   * Render the button full width.
   */
  block?: boolean | undefined;
  /**
   * Set loading state automatically based on the `@click` promise state
   */
  loadingAuto?: boolean | undefined;
  /**
   * Display an avatar on the left side.
   */
  avatar?: AvatarProps | undefined;
  /**
   * When `true`, the icon will be displayed on the left side.
   */
  leading?: boolean | undefined;
  /**
   * Display an icon on the left side.
   */
  leadingIcon?: any;
  /**
   * When `true`, the icon will be displayed on the right side.
   */
  trailing?: boolean | undefined;
  /**
   * Display an icon on the right side.
   */
  trailingIcon?: any;
  /**
   * When `true`, the loading icon will be displayed.
   */
  loading?: boolean | undefined;
  /**
   * The icon when the `loading` prop is `true`.
   * @default appConfig.ui.icons.loading
   */
  loadingIcon?: any;
}
```

> [!NOTE]
> See: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attributes
> 
> This component also supports all native `<button>` HTML attributes.

### Slots

```ts
/**
 * Slots for the ContentSearchButton component
 */
interface ContentSearchButtonSlots {
  leading(): any;
  default(): any;
  trailing(): any;
}
```

## Theme

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    contentSearchButton: {
      slots: {
        base: '',
        label: '',
        trailing: 'hidden lg:flex items-center gap-0.5 ms-auto'
      },
      variants: {
        collapsed: {
          true: {
            label: 'hidden',
            trailing: 'lg:hidden'
          }
        }
      }
    }
  }
})
```

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/content/ContentSearchButton.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/content/content-search-button.ts).

---

- [Button](https://ui.nuxt.com/docs/components/button)
- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/content/ContentSearchButton.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
