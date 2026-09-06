---
title: "ProseField"
description: "Document API parameters, props, and configuration options clearly."
canonical_url: "https://ui.nuxt.com/docs/typography/field"
---
# ProseField

> Document API parameters, props, and configuration options clearly.

## Usage

A field, prop or parameter to display in your content.

```mdc
::field{name="name" type="string" required}
The `description` can be set as prop or in the default slot with full **markdown** support.
::
```

## API

### Props

```ts
/**
 * Props for the ProseProseField component
 */
interface ProseProseFieldProps {
  /**
   * The element or component this component should render as.
   * @default 'div'
   */
  as?: any;
  /**
   * The name of the field.
   */
  name?: string | undefined;
  /**
   * Expected type of the field's value
   */
  type?: string | undefined;
  /**
   * Description of the field
   */
  description?: string | undefined;
  /**
   * Indicate whether the field is required
   */
  required?: boolean | undefined;
  ui?: { root?: SlotClass; container?: SlotClass; name?: SlotClass; wrapper?: SlotClass; required?: SlotClass; type?: SlotClass; description?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseField component
 */
interface ProseFieldSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseField.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-field.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Field.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
