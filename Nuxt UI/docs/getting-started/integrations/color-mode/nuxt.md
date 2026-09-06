---
title: "Color Mode"
description: "Nuxt UI integrates with Nuxt Color Mode to allow for easy switching between light and dark themes."
canonical_url: "https://ui.nuxt.com/docs/getting-started/integrations/color-mode/nuxt"
---
# Color Mode

> Nuxt UI integrates with Nuxt Color Mode to allow for easy switching between light and dark themes.

> [!NOTE]
> See: /docs/getting-started/integrations/color-mode/vue
> 
> Looking for the **Vue** version?

## Usage

Nuxt UI automatically registers the [`@nuxtjs/color-mode`](https://github.com/nuxt-modules/color-mode) module for you, so there's no additional setup required.

### Components

You can use the built-in [ColorModeAvatar](https://ui.nuxt.com/docs/components/color-mode-avatar) or [ColorModeImage](https://ui.nuxt.com/docs/components/color-mode-image) components to display different images for light and dark mode and the [ColorModeButton](https://ui.nuxt.com/docs/components/color-mode-button), [ColorModeSwitch](https://ui.nuxt.com/docs/components/color-mode-switch) or [ColorModeSelect](https://ui.nuxt.com/docs/components/color-mode-select) components to switch between light and dark modes.

You can also use the [useColorMode](https://color-mode.nuxtjs.org/usage/basic) composable to build your own custom component:

```vue [ColorModeButton.vue]
<script setup lang="ts">
const colorMode = useColorMode()

const isDark = computed({
  get() {
    return colorMode.value === 'dark'
  },
  set(_isDark) {
    colorMode.preference = _isDark ? 'dark' : 'light'
  }
})
</script>

<template>
  <ClientOnly v-if="!colorMode?.forced">
    <UButton
      :icon="isDark ? 'i-lucide-moon' : 'i-lucide-sun'"
      color="neutral"
      variant="ghost"
      :aria-label="`Switch to ${isDark ? 'light' : 'dark'} mode`"
      @click="isDark = !isDark"
    />

    <template #fallback>
      <div class="size-8" />
    </template>
  </ClientOnly>
</template>
```

### Configuration

You can disable the `@nuxtjs/color-mode` module with the `ui.colorMode` option in your `nuxt.config.ts`:

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  modules: ['@nuxt/ui'],
  css: ['~/assets/css/main.css'],
  ui: {
    colorMode: false
  }
})
```

---

- [nuxtjs/color-mode](https://github.com/nuxt-modules/color-mode)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
