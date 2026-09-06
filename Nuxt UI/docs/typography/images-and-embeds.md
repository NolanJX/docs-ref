---
title: "Images and embeds"
description: "Responsive images, videos, and rich media embeds to enhance and illustrate your documentation."
canonical_url: "https://ui.nuxt.com/docs/typography/images-and-embeds"
---
# Images and embeds

> Responsive images, videos, and rich media embeds to enhance and illustrate your documentation.

## Images

Responsive images with automatic optimization and interactive zoom functionality.

```vue
<template>
  <p>
    <img alt="Image" src="/assets/templates/nuxt/dashboard-dark.png" />
  </p>
</template>
```

```mdc
![Image](/assets/templates/nuxt/dashboard-dark.png)
```

:::

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {
      img: {
        slots: {
          base: 'rounded-md',
          overlay: 'fixed inset-0 bg-default/75 backdrop-blur-sm will-change-opacity',
          content: 'fixed inset-0 flex items-center justify-center cursor-zoom-out focus:outline-none',
          zoomedImage: 'w-full h-auto max-w-[95vw] max-h-[95vh] object-contain rounded-md'
        },
        variants: {
          zoom: {
            true: 'will-change-transform'
          },
          open: {
            true: ''
          },
          width: {
            false: 'w-full'
          }
        },
        compoundVariants: [
          {
            zoom: true,
            open: false,
            class: 'cursor-zoom-in'
          }
        ]
      }
    }
  }
})
```

**Nuxt:**

> [!NOTE]
> 
> If [`@nuxt/image`](https://image.nuxt.com/get-started/installation) is installed, the `<NuxtImg>` component will be used instead of the native `img` tag for enhanced performance and optimization.

### Zoom

By default, images support interactive zoom: clicking an image opens it in a modal overlay with smooth transitions, allowing users to examine details more closely.

To prevent zoom on a specific image, add the `:zoom="false"` attribute.

```mdc
![Image without zoom](/assets/templates/nuxt/dashboard-dark.png){:zoom="false"}
```

### Size

Images automatically adapt to their container while maintaining aspect ratio. You can control sizing through markdown or HTML attributes when needed.

```mdc
![Small Image](/assets/templates/nuxt/dashboard-dark.png){width="300"}
```

## Iframes

Easily embed interactive content like CodeSandbox, Figma, or YouTube.

### YouTube

```html
<iframe src="https://www.youtube-nocookie.com/embed/_eQxomah-nA?si=pDSzchUBDKb2NQu7" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen style="aspect-ratio: 16/9; width: 100%;"></iframe>
```

### CodeSandbox

```html
<iframe src="https://codesandbox.io/p/devbox/nuxt-ui-xgrzw5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen style="aspect-ratio: 16/9; width: 100%;"></iframe>
```

### Figma

```html
<iframe style="border: 1px solid rgba(0, 0, 0, 0.1); width: 100%; height: 450px;" src="https://embed.figma.com/file/1544369209862884086/hf_embed?community_viewer=true&embed_host=fastma&fuid=960610330589944894&kind=file&page-selector=0&viewer=1" allowfullscreen></iframe>
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
