---
title: "Lists and tables"
description: "Organize information with styled lists and responsive tables for clear, consistent readability."
canonical_url: "https://ui.nuxt.com/docs/typography/lists-and-tables"
---
# Lists and tables

> Organize information with styled lists and responsive tables for clear, consistent readability.

## Lists

Markdown supports unordered, ordered, and nested lists for various content needs.

### Unordered lists

Use unordered lists for items without a specific sequence. Start each item with a `-` symbol.

```mdc
- I'm a list item.
- I'm another list item.
- I'm the last list item.
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      ul: {
        base: 'list-disc ps-6 my-5 marker:text-(--ui-border-accented)'
      }
    }
  }
})
```

### Ordered lists

Use ordered lists when item order matters, like steps in a process. Start each item with a number.

```mdc
1. I'm a list item.
2. I'm another list item.
3. I'm the last list item.
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      ol: {
        base: 'list-decimal ps-6 my-5 marker:text-muted'
      }
    }
  }
})
```

### Nested lists

Create hierarchical lists with sub-items for complex structures. Indent sub-items by four spaces for nesting.

```mdc
- I'm a list item.
  - I'm a nested list item.
  - I'm another nested list item.
- I'm another list item.
  - Another nested item
    - Deep nested item
    - Another deep nested item
  - Back to second level
```

### Mixed lists

You can combine ordered and unordered lists for complex hierarchies.

```mdc
1. First major step
   - Sub-requirement A
   - Sub-requirement B
2. Second major step
   - Another sub-item
   - Final sub-item
3. Final step
```

## Tables

Present structured data in rows and columns clearly. Tables are ideal for comparing data or listing properties.

```mdc
| Prop    | Default   | Type                     |
|---------|-----------|--------------------------|
| `name`  |           | `string`{lang="ts-type"} |
| `size`  | `md`      | `string`{lang="ts-type"} |
| `color` | `neutral` | `string`{lang="ts-type"} |
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      table: {
        slots: {
          root: 'relative my-5 overflow-x-auto rounded-md outline-primary/25 focus-visible:outline-3',
          base: 'w-full border-separate border-spacing-0 rounded-md'
        }
      }
    }
  }
})
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
