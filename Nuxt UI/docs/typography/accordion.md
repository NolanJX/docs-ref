---
title: "ProseAccordion"
description: "Create expandable content sections for better information organization."
canonical_url: "https://ui.nuxt.com/docs/typography/accordion"
---
# ProseAccordion

> Create expandable content sections for better information organization.

## Usage

Use the `accordion` and `accordion-item` components to display an [Accordion](https://ui.nuxt.com/docs/components/accordion) in your content.

```mdc
::accordion
---
defaultValue:
  - '1'
---

::accordion-item{label="Is Nuxt UI free to use?" icon="i-lucide-circle-help"}
Yes! Nuxt UI is completely free and open source under the MIT license. All 125+ components are available to everyone.
::

::accordion-item{label="Can I use Nuxt UI with Vue without Nuxt?" icon="i-lucide-circle-help"}
Yes! While optimized for Nuxt, Nuxt UI works perfectly with standalone Vue projects via our Vite plugin. You can follow the [installation guide](/docs/getting-started/installation/vue) to get started.
::

::accordion-item{label="Is Nuxt UI production-ready?" icon="i-lucide-circle-help"}
Yes! Nuxt UI is used in production by thousands of applications with extensive tests, regular updates, and active maintenance.
::

::
```

## API

### Props

```ts
/**
 * Props for the ProseProseAccordion component
 */
interface ProseProseAccordionProps {
  /**
   * @default 'multiple'
   */
  type?: "multiple" | "single" | undefined;
  ui?: { root?: SlotClass; trigger?: SlotClass; } & { root?: SlotClass; item?: SlotClass; header?: SlotClass; trigger?: SlotClass; content?: SlotClass; body?: SlotClass; leadingIcon?: SlotClass; trailingIcon?: SlotClass; label?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseAccordion component
 */
interface ProseAccordionSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseAccordion.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-accordion.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Accordion.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
