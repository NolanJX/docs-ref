---
title: "ProseFieldGroup"
description: "Group related fields together for comprehensive API documentation."
canonical_url: "https://ui.nuxt.com/docs/typography/field-group"
---
# ProseFieldGroup

> Group related fields together for comprehensive API documentation.

## Usage

Group fields together in a list.

```mdc
::field-group
  ::field{name="analytics" type="boolean"}
    Defaults to `false`. Enables analytics for your project (coming soon).
  ::

  ::field{name="blob" type="boolean"}
    Defaults to `false`. Enables blob storage to store static assets, such as images, videos and more.
  ::

  ::field{name="cache" type="boolean"}
    Defaults to `false`. Enables cache storage to cache your server route responses or functions using Nitro's `cachedEventHandler` and `cachedFunction`.
  ::

  ::field{name="database" type="boolean"}
    Defaults to `false`. Enables SQL database to store your application's data.
  ::
::
```

## API

### Props

```ts
/**
 * Props for the ProseProseFieldGroup component
 */
interface ProseProseFieldGroupProps {
  /**
   * The element or component this component should render as.
   * @default 'div'
   */
  as?: any;
  ui?: { base?: any; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseFieldGroup component
 */
interface ProseFieldGroupSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseFieldGroup.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-field-group.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/FieldGroup.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
