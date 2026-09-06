---
title: "useScrollShadow"
description: "A composable to apply scroll shadow effects on any scrollable element."
canonical_url: "https://ui.nuxt.com/docs/composables/use-scroll-shadow"
---
# useScrollShadow

> A composable to apply scroll shadow effects on any scrollable element.

## Usage

Use the auto-imported `useScrollShadow` composable to apply fade shadows on the edges of a scrollable element, indicating that more content is available in the scroll direction.

```vue [UseScrollShadowExample.vue]
<script setup lang="ts">
const el = useTemplateRef('el')

const { style } = useScrollShadow(el)
</script>

<template>
  <div class="max-w-sm bg-elevated/50 rounded-lg">
    <div
      ref="el"
      :style="style"
      class="h-72 p-4 space-y-4 overflow-y-auto"
    >
      <p v-for="i in 6" :key="i">
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam pulvinar risus non risus hendrerit venenatis. Pellentesque sit amet hendrerit risus, sed porttitor quam. Morbi accumsan cursus enim, sed ultricies sapien.
      </p>
    </div>
  </div>
</template>
```

- Uses CSS `mask-image` to fade content at the edges rather than overlay elements, so it works on any background.
- Automatically detects whether the element is overflowing and only applies shadows when needed.
- Supports both vertical and horizontal orientations.

## API

`useScrollShadow(element, options?)`

### Parameters

**element** (`MaybeRef<HTMLElement | null | undefined>`) *required*: A template ref or reactive reference to the scrollable element.

**options** (`UseScrollShadowOptions`): Configuration options for the scroll shadow.The shadow size in pixels.The scroll direction to apply shadows.

### Return

**style** (`ComputedRef<CSSProperties | undefined>`): A reactive style object to bind on the scrollable element with :style. Contains maskImage when shadows are active, undefined otherwise.

**isOverflowing** (`ComputedRef<boolean>`): Whether the element's content overflows its visible area.

**arrivedState** (`{ top: boolean, bottom: boolean, left: boolean, right: boolean }`): Reactive scroll arrival state from useScroll.

## Examples

### Horizontal

Use the `orientation` option for horizontally scrollable containers:

```vue
<script setup lang="ts">
const el = useTemplateRef('el')

const { style } = useScrollShadow(el, { orientation: 'horizontal' })
</script>

<template>
  <div ref="el" class="overflow-x-auto whitespace-nowrap" :style="style">
    <!-- Horizontally scrollable content -->
  </div>
</template>
```

### Custom size

Use the `size` option to change the shadow size in pixels:

```vue
<script setup lang="ts">
const el = useTemplateRef('el')

const { style } = useScrollShadow(el, { size: 48 })
</script>

<template>
  <div ref="el" class="max-h-[300px] overflow-y-auto" :style="style">
    <!-- Scrollable content -->
  </div>
</template>
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
