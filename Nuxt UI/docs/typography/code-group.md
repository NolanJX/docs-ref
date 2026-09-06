---
title: "ProseCodeGroup"
description: "Group multiple code examples in tabbed interfaces for easy comparison."
canonical_url: "https://ui.nuxt.com/docs/typography/code-group"
---
# ProseCodeGroup

> Group multiple code examples in tabbed interfaces for easy comparison.

## Usage

Wrap your code blocks around a `code-group` component to group them together in tabs.

````mdc
::code-group

```bash [pnpm]
pnpm add @nuxt/ui
```

```bash [yarn]
yarn add @nuxt/ui
```

```bash [npm]
npm install @nuxt/ui
```

```bash [bun]
bun add @nuxt/ui
```

::
````

> [!NOTE]
> See: /docs/typography/code#code-blocks
> 
> Like the `ProsePre` component, the `CodeGroup` handles filenames, icons and copy button.

## API

### Props

```ts
/**
 * Props for the ProseProseCodeGroup component
 */
interface ProseProseCodeGroupProps {
  /**
   * The default tab to select.
   * @default '0'
   */
  defaultValue?: string | undefined;
  /**
   * Sync the selected tab with a local storage key.
   */
  sync?: string | undefined;
  ui?: { root?: SlotClass; list?: SlotClass; indicator?: SlotClass; trigger?: SlotClass; triggerIcon?: SlotClass; triggerLabel?: SlotClass; } | undefined;
  modelValue?: string | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseCodeGroup component
 */
interface ProseCodeGroupSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseCodeGroup.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-code-group.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/CodeGroup.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
