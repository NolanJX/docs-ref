---
title: "ProseCallout"
description: "Highlight important information with eye-catching colored boxes and icons."
canonical_url: "https://ui.nuxt.com/docs/typography/callout"
---
# ProseCallout

> Highlight important information with eye-catching colored boxes and icons.

## Usage

Use markdown in the default slot of the `callout` component to add eye-catching context to your content.

```mdc
::callout
This is a `callout` with full **markdown** support.
::
```

### Icon

Use the `icon` prop to display an icon next to the content.

```mdc
::callout{icon="i-lucide-square-play"}
This is a `callout` with an icon.
::
```

### Color

Use the `color` prop to change the color of the callout.

```mdc
::callout{icon="i-lucide-info" color="info"}
This is a `callout` with a custom color.
::
```

### Link

You can pass any property from the [`<NuxtLink>`](https://nuxt.com/docs/api/components/nuxt-link) component such as `to` and `target` to make the callout a link.

```mdc
::callout{icon="i-lucide-square-play" to="/docs/getting-started/installation/nuxt" color="neutral"}
Learn how to install `@nuxt/ui` in your project.
::
```

## Shortcuts

You can also use the `note`, `tip`, `warning` and `caution` shortcuts with pre-defined icons and colors.

```mdc
::note
Here's some additional information.
::

::tip
Here's a helpful suggestion.
::

::warning
Be careful with this action as it might have unexpected results.
::

::caution
This action cannot be undone.
::
```

## API

### Props

```ts
/**
 * Props for the ProseProseCallout component
 */
interface ProseProseCalloutProps {
  to?: string | it | et | undefined;
  target?: null | "_blank" | "_parent" | "_self" | "_top" | string & {} | undefined;
  icon?: any;
  /**
   * @default 'neutral'
   */
  color?: "error" | "primary" | "secondary" | "success" | "info" | "warning" | "neutral" | undefined;
  ui?: { base?: SlotClass; icon?: SlotClass; externalIcon?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseCallout component
 */
interface ProseCalloutSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseCallout.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-callout.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Callout.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
