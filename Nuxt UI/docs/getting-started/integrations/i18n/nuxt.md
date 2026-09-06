---
title: "Internationalization (i18n)"
description: "Nuxt UI supports 50+ locales and multi-directional (LTR/RTL) internationalization."
canonical_url: "https://ui.nuxt.com/docs/getting-started/integrations/i18n/nuxt"
---
# Internationalization (i18n)

> Nuxt UI supports 50+ locales and multi-directional (LTR/RTL) internationalization.

> [!NOTE]
> See: /docs/getting-started/integrations/i18n/vue
> 
> Looking for the **Vue** version?

## Usage

> [!NOTE]
> See: /docs/components/app
> 
> Nuxt UI provides an **App** component that wraps your app to provide global configurations, including the `locale` prop.

### Locale

Use the `locale` prop with the locale you want to use from `@nuxt/ui/locale`:

```vue [app.vue]
<script setup lang="ts">
import { fr } from '@nuxt/ui/locale'
</script>

<template>
  <UApp :locale="fr">
    <NuxtPage />
  </UApp>
</template>
```

> [!TIP]
> 
> Each locale has a `code` property (e.g. `en`, `en-GB`, `fr`) that determines the date/time format in components like [Calendar](https://ui.nuxt.com/docs/components/calendar), [InputDate](https://ui.nuxt.com/docs/components/input-date) and [InputTime](https://ui.nuxt.com/docs/components/input-time).

### Custom locale

You can create your own locale using the [defineLocale](https://ui.nuxt.com/docs/composables/define-locale) utility:

```vue [app.vue]
<script setup lang="ts">
import type { Messages } from '@nuxt/ui'

const locale = defineLocale<Messages>({
  name: 'My custom locale',
  code: 'en',
  dir: 'ltr',
  messages: {
    // implement pairs
  }
})
</script>

<template>
  <UApp :locale="locale">
    <NuxtPage />
  </UApp>
</template>
```

> [!TIP]
> 
> Look at the `code` parameter, there you need to pass the iso code of the language. Example:
> 
> - `hi` Hindi (language)
> - `de-AT`: German (language) as used in Austria (region)

### Extend locale

You can customize an existing locale by overriding its `messages` or `code` using the [extendLocale](https://ui.nuxt.com/docs/composables/extend-locale) utility:

```vue [app.vue]
<script setup lang="ts">
import { en } from '@nuxt/ui/locale'

const locale = extendLocale(en, {
  code: 'en-AU',
  messages: {
    commandPalette: {
      placeholder: 'Search a component...'
    }
  }
})
</script>

<template>
  <UApp :locale="locale">
    <NuxtPage />
  </UApp>
</template>
```

### Dynamic locale

To dynamically switch between languages, you can use the [Nuxt I18n](https://i18n.nuxtjs.org/) module.

#### Install the Nuxt I18n package

```bash [pnpm]
pnpm add @nuxtjs/i18n
```

```bash [yarn]
yarn add @nuxtjs/i18n
```

```bash [npm]
npm install @nuxtjs/i18n
```

```bash [bun]
bun add @nuxtjs/i18n
```

#### Add the Nuxt I18n module in your `nuxt.config.ts`

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  modules: [
    '@nuxt/ui',
    '@nuxtjs/i18n'
  ],
  css: ['~/assets/css/main.css'],
  i18n: {
    locales: [{
      code: 'de',
      name: 'Deutsch'
    }, {
      code: 'en',
      name: 'English'
    }, {
      code: 'fr',
      name: 'Français'
    }]
  }
})
```

#### Set the `locale` prop using `useI18n`

```vue [app.vue]
<script setup lang="ts">
import * as locales from '@nuxt/ui/locale'

const { locale } = useI18n()
</script>

<template>
  <UApp :locale="locales[locale]">
    <NuxtPage />
  </UApp>
</template>
```

#### Automatic link localization `4.7+`

When `@nuxtjs/i18n` is installed, the [Link](https://ui.nuxt.com/docs/components/link) component automatically localizes internal links using the `$localePath` helper. This means you don't need to manually wrap your links with `localePath()` or `localeRoute()`.

```vue
<template>
  <!-- Automatically becomes /en/about or /fr/about based on current locale -->
  <ULink to="/about">About</ULink>
  <UButton to="/contact">Contact</UButton>
</template>
```

> [!TIP]
> 
> External links and absolute URLs are automatically detected and skip localization. You can still manually use `localePath()` or `localeRoute()` if needed.

### Dynamic direction

Each locale has a `dir` property which will be used by the `App` component to set the directionality of all components.

In a multilingual application, you might want to set the `lang` and `dir` attributes on the `<html>` element dynamically based on the user's locale, which you can do with the [useHead](https://nuxt.com/docs/api/composables/use-head) composable:

```vue [app.vue]
<script setup lang="ts">
import * as locales from '@nuxt/ui/locale'

const { locale } = useI18n()

const lang = computed(() => locales[locale.value].code)
const dir = computed(() => locales[locale.value].dir)

useHead({
  htmlAttrs: {
    lang,
    dir
  }
})
</script>

<template>
  <UApp :locale="locales[locale]">
    <NuxtPage />
  </UApp>
</template>
```

## Supported languages

*See the full list of supported languages on the documentation website.*


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
