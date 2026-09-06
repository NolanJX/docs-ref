---
title: "ProseCollapsible"
description: "Toggle content visibility with smooth expand and collapse animations."
canonical_url: "https://ui.nuxt.com/docs/typography/collapsible"
---
# ProseCollapsible

> Toggle content visibility with smooth expand and collapse animations.

## Usage

Wrap your content with the `collapsible` component to display a [Collapsible](https://ui.nuxt.com/docs/components/collapsible) in your content.

```mdc
::collapsible

| Prop    | Default   | Type                     |
|---------|-----------|--------------------------|
| `name`  |           | `string`{lang="ts-type"} |
| `size`  | `md`      | `string`{lang="ts-type"} |
| `color` | `neutral` | `string`{lang="ts-type"} |

::
```

## API

### Props

```ts
/**
 * Props for the ProseProseCollapsible component
 */
interface ProseProseCollapsibleProps {
  /**
   * The icon displayed to toggle the collapsible.
   * @default appConfig.ui.icons.chevronDown
   */
  icon?: any;
  /**
   * The name displayed in the trigger label.
   * @default t('prose.collapsible.name')
   */
  name?: string | undefined;
  /**
   * The text displayed when the collapsible is open.
   * @default t('prose.collapsible.openText')
   */
  openText?: string | undefined;
  /**
   * The text displayed when the collapsible is closed.
   * @default t('prose.collapsible.closeText')
   */
  closeText?: string | undefined;
  ui?: { root?: SlotClass; trigger?: SlotClass; triggerIcon?: SlotClass; triggerLabel?: SlotClass; content?: SlotClass; } & { root?: SlotClass; content?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseCollapsible component
 */
interface ProseCollapsibleSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseCollapsible.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-collapsible.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Collapsible.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
