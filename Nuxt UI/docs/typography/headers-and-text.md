---
title: "Headers and text"
description: "Beautifully styled headings, paragraphs, text formatting, and links for optimal readability."
canonical_url: "https://ui.nuxt.com/docs/typography/headers-and-text"
---
# Headers and text

> Beautifully styled headings, paragraphs, text formatting, and links for optimal readability.

## Headings

Use headings to organize your content and make it easier to read.

Headings can be wrapped in an anchor link when they have an `id`, with a hash icon shown on hover for `H2` and `H3` (on large screens) so readers can link directly to a section.

Anchor links are enabled by default for `H2` to `H4` when using `@nuxt/content` or `@nuxtjs/mdc`, and disabled otherwise. Use the [`Theme`](https://ui.nuxt.com/docs/components/theme) component with the `anchor` prop to toggle them for a section of your app:

```vue
<template>
  <UTheme :props="{ prose: { h2: { anchor: true }, h3: { anchor: true }, h4: { anchor: true } } }">
    <!-- your rendered markdown -->
  </UTheme>
</template>
```

**Nuxt:**

> [!NOTE]
> 
> When using `@nuxt/content`, anchor links are enabled for `H2`, `H3` and `H4` by default. You can control their [generation](https://content.nuxt.com/docs/getting-started/configuration#anchorlinks) (for example, to disable them for AI chat interfaces) as well as the [toc generation](https://content.nuxt.com/docs/getting-started/configuration#toc) in your `nuxt.config.ts`:
> 
> ```ts [nuxt.config.ts]
> export default defineNuxtConfig({
>   content: {
>     renderer: {
>       anchorLinks: false
>     },
>     build: {
>       markdown: {
>         toc: {
>           depth: 3
>         }
>       }
>     }
>   }
> })
> ```

### Heading 1

```mdc
# Nuxt UI
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      h1: {
        slots: {
          base: 'text-4xl text-highlighted font-bold mb-8 scroll-mt-[calc(45px+var(--ui-header-height))] lg:scroll-mt-(--ui-header-height)',
          link: 'inline-flex items-center gap-2'
        }
      }
    }
  }
})
```

### Heading 2

```mdc
## What's new in v4?
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      h2: {
        slots: {
          base: [
            'relative text-2xl text-highlighted font-bold mt-12 mb-6 scroll-mt-[calc(48px+45px+var(--ui-header-height))] lg:scroll-mt-[calc(48px+var(--ui-header-height))] [&>a]:rounded-sm [&>a]:outline-primary/25 [&>a]:focus-visible:outline-3 [&>a>code]:border-dashed hover:[&>a>code]:border-primary hover:[&>a>code]:text-primary [&>a>code]:text-xl/7 [&>a>code]:font-bold',
            '[&>a>code]:transition-colors'
          ],
          leading: [
            'absolute -ms-8 top-1 opacity-0 group-hover:opacity-100 group-focus:opacity-100 p-1 bg-elevated group-hover:text-primary group-focus:text-primary rounded-md hidden lg:flex text-muted',
            'transition'
          ],
          leadingIcon: 'size-4 shrink-0',
          link: 'group lg:after:absolute lg:after:inset-y-0 lg:after:-inset-s-2 lg:after:w-2'
        }
      }
    }
  }
})
```

### Heading 3

```mdc
### Enhanced components
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      h3: {
        slots: {
          base: [
            'relative text-xl text-highlighted font-bold mt-8 mb-3 scroll-mt-[calc(32px+45px+var(--ui-header-height))] lg:scroll-mt-[calc(32px+var(--ui-header-height))] [&>a]:rounded-sm [&>a]:outline-primary/25 [&>a]:focus-visible:outline-3 [&>a>code]:border-dashed hover:[&>a>code]:border-primary hover:[&>a>code]:text-primary [&>a>code]:text-lg/6 [&>a>code]:font-bold',
            '[&>a>code]:transition-colors'
          ],
          leading: [
            'absolute -ms-8 top-0.5 opacity-0 group-hover:opacity-100 group-focus:opacity-100 p-1 bg-elevated group-hover:text-primary group-focus:text-primary rounded-md hidden lg:flex text-muted',
            'transition'
          ],
          leadingIcon: 'size-4 shrink-0',
          link: 'group lg:after:absolute lg:after:inset-y-0 lg:after:-inset-s-2 lg:after:w-2'
        }
      }
    }
  }
})
```

### Heading 4

```mdc
#### Getting started
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      h4: {
        slots: {
          base: 'text-lg text-highlighted font-bold mt-6 mb-2 scroll-mt-[calc(24px+45px+var(--ui-header-height))] lg:scroll-mt-[calc(24px+var(--ui-header-height))] [&>a]:rounded-sm [&>a]:outline-primary/25 [&>a]:focus-visible:outline-3',
          link: ''
        }
      }
    }
  }
})
```

## Text formatting

Structure your content with clear paragraphs and consistent text formatting for better readability.

### Paragraph

```mdc
Nuxt UI provides a comprehensive collection of Vue components, composables and utilities for building modern, accessible applications with consistent design and enhanced user experience.
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      p: {
        base: 'my-5 leading-7 text-pretty'
      }
    }
  }
})
```

### Strong

```mdc
**Strong text**
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      strong: {
        base: ''
      }
    }
  }
})
```

### Emphasis

```mdc
*Emphasized text*
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      em: {
        base: ''
      }
    }
  }
})
```

## Links

To create a link, wrap the link text in brackets followed by the URL in parentheses. Works for both external and internal links.

```mdc
[Nuxt documentation](https://nuxt.com)
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      a: {
        base: [
          'text-primary border-b border-transparent hover:border-primary font-medium rounded-xs outline-primary/25 focus-visible:outline-3 focus-visible:has-[>code]:outline-0 [&>code]:border-dashed [&>code]:outline-primary/25 focus-visible:[&>code]:outline-3 hover:[&>code]:border-primary hover:[&>code]:text-primary focus-visible:[&>code]:border-primary focus-visible:[&>code]:text-primary',
          'transition-colors [&>code]:transition-colors'
        ]
      }
    }
  }
})
```

## Blockquotes

Use blockquotes to highlight important information or quotes.

```mdc
> Nuxt UI automatically adapts to your theme settings, ensuring consistent typography across your entire application.
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      blockquote: {
        base: 'border-s-4 border-accented ps-4 italic'
      }
    }
  }
})
```

## Horizontal rules

Use horizontal rules to visually separate content sections.

```mdc
---
```

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      hr: {
        base: 'border-t border-default my-12'
      }
    }
  }
})
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
