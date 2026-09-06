---
title: "ProseTabs"
description: "Organize related content in interactive tabbed interfaces."
canonical_url: "https://ui.nuxt.com/docs/typography/tabs"
---
# ProseTabs

> Organize related content in interactive tabbed interfaces.

## Usage

Use the `tabs` and `tabs-item` components to display [Tabs](https://ui.nuxt.com/docs/components/tabs) in your content.

````mdc
::tabs

:::tabs-item{label="Code" icon="i-lucide-code"}

```mdc
::callout
Lorem velit voluptate ex reprehenderit ullamco et culpa.
::
```

:::

:::tabs-item{label="Preview" icon="i-lucide-eye"}

::callout
Lorem velit voluptate ex reprehenderit ullamco et culpa.
::

:::

::
````

## API

### Props

```ts
/**
 * Props for the ProseProseTabs component
 */
interface ProseProseTabsProps {
  /**
   * The default tab to select.
   * @default '0'
   */
  defaultValue?: string | undefined;
  /**
   * Sync the selected tab with a local storage key.
   */
  sync?: string | undefined;
  /**
   * The hash to scroll to when the tab is selected.
   */
  hash?: string | undefined;
  ui?: { root?: SlotClass; } & { root?: SlotClass; list?: SlotClass; indicator?: SlotClass; trigger?: SlotClass; leadingIcon?: SlotClass; leadingAvatar?: SlotClass; leadingAvatarSize?: SlotClass; label?: SlotClass; trailingBadge?: SlotClass; trailingBadgeSize?: SlotClass; content?: SlotClass; } | undefined;
  modelValue?: string | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseTabs component
 */
interface ProseTabsSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseTabs.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-tabs.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Tabs.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
