---
title: "ProseCardGroup"
description: "Organize multiple cards in responsive grid layouts for better content presentation."
canonical_url: "https://ui.nuxt.com/docs/typography/card-group"
---
# ProseCardGroup

> Organize multiple cards in responsive grid layouts for better content presentation.

## Usage

Wrap your `card` components with the `card-group` component to group them together in a grid layout.

```mdc
::card-group

::card
---
title: Dashboard
icon: i-simple-icons-github
to: https://github.com/nuxt-ui-templates/dashboard
target: _blank
---
A dashboard with multi-column layout.
::

::card
---
title: SaaS
icon: i-simple-icons-github
to: https://github.com/nuxt-ui-templates/saas
target: _blank
---
A template with landing, pricing, docs and blog.
::

::card
---
title: Docs
icon: i-simple-icons-github
to: https://github.com/nuxt-ui-templates/docs
target: _blank
---
A documentation with `@nuxt/content`.
::

::card
---
title: Landing
icon: i-simple-icons-github
to: https://github.com/nuxt-ui-templates/landing
target: _blank
---
A landing page you can use as starting point.
::

::
```

## API

### Props

```ts
/**
 * Props for the ProseProseCardGroup component
 */
interface ProseProseCardGroupProps {
  ui?: { base?: any; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseCardGroup component
 */
interface ProseCardGroupSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseCardGroup.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-card-group.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/CardGroup.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
