---
title: "Color Mode"
description: "Nuxt UI integrates with VueUse to allow for easy switching between light and dark themes."
canonical_url: "https://ui.nuxt.com/docs/getting-started/integrations/color-mode/vue"
---
# Color Mode

> Nuxt UI integrates with VueUse to allow for easy switching between light and dark themes.

> [!NOTE]
> See: /docs/getting-started/integrations/color-mode/nuxt
> 
> Looking for the **Nuxt** version?

## Usage

Nuxt UI automatically registers the [useDark](https://vueuse.org/core/useDark) composable as a Vue plugin, so there's no additional setup required.

### Components

You can use the built-in [ColorModeAvatar](https://ui.nuxt.com/docs/components/color-mode-avatar) or [ColorModeImage](https://ui.nuxt.com/docs/components/color-mode-image) components to display different images for light and dark mode and the [ColorModeButton](https://ui.nuxt.com/docs/components/color-mode-button), [ColorModeSwitch](https://ui.nuxt.com/docs/components/color-mode-switch) or [ColorModeSelect](https://ui.nuxt.com/docs/components/color-mode-select) components to switch between light and dark modes.

You can also use the [useColorMode](https://vueuse.org/core/useColorMode) composable to build your own custom component:

```vue [ColorModeButton.vue]
<script setup lang="ts">
import { computed } from 'vue'
import { useColorMode } from '@vueuse/core'

const colorMode = useColorMode()

const isDark = computed({
  get() {
    return colorMode.value === 'dark'
  },
  set(_isDark: boolean) {
    colorMode.value = _isDark ? 'dark' : 'light'
  }
})
</script>

<template>
  <UButton
    :icon="isDark ? 'i-lucide-moon' : 'i-lucide-sun'"
    color="neutral"
    variant="ghost"
    :aria-label="`Switch to ${isDark ? 'light' : 'dark'} mode`"
    @click="isDark = !isDark"
  />
</template>
```

### Configuration

You can disable this plugin with the `colorMode` option in your `vite.config.ts`:

```ts [vite.config.ts]
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ui from '@nuxt/ui/vite'

export default defineConfig({
  plugins: [
    vue(),
    ui({
      colorMode: false
    })
  ]
})
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
