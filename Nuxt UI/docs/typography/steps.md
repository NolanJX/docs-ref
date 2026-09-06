---
title: "ProseSteps"
description: "Transform headings into numbered step-by-step guides and tutorials."
canonical_url: "https://ui.nuxt.com/docs/typography/steps"
---
# ProseSteps

> Transform headings into numbered step-by-step guides and tutorials.

## Usage

Wrap your headings with the Steps component to display a list of steps.

Use the `level` prop to define which heading will be used for the steps.

````mdc
::steps{level="4"}

#### Add the Nuxt UI module in your `nuxt.config.ts`

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  modules: ['@nuxt/ui']
})
```

#### Import Tailwind CSS in your CSS

```css [app/assets/css/main.css]
@import "tailwindcss";
```

#### Start your development server

```bash
npm run dev
```

::
````

## API

### Props

```ts
/**
 * Props for the ProseProseSteps component
 */
interface ProseProseStepsProps {
  /**
   * The heading level to apply to the steps.
   * @default '3'
   */
  level?: "3" | "2" | "4" | undefined;
  ui?: { base?: any; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProseSteps component
 */
interface ProseStepsSlots {
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

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProseSteps.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-steps.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Steps.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
