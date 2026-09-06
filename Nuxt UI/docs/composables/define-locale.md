---
title: "defineLocale"
description: "A utility to create a custom locale for your app."
canonical_url: "https://ui.nuxt.com/docs/composables/define-locale"
---
# defineLocale

> A utility to create a custom locale for your app.

## Usage

Use the auto-imported `defineLocale` utility to create a custom locale with your own translations.

```vue
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

**Nuxt:**

> [!TIP]
> See: /docs/getting-started/integrations/i18n/nuxt
> 
> Learn more about internationalization in the **i18n integration** documentation.

**Vue:**

> [!TIP]
> See: /docs/getting-started/integrations/i18n/vue
> 
> Learn more about internationalization in the **i18n integration** documentation.

## API

`defineLocale<M>(options: DefineLocaleOptions<M>): Locale<M>`

Creates a new locale object with the provided options.

#### Parameters

**options** (`DefineLocaleOptions<M>`) *required*: The locale configuration object with the following properties:The display name of the locale (e.g. 'English', 'Français').The ISO code of the locale (e.g. 'en', 'fr', 'de-AT').The text direction of the locale. Defaults to 'ltr'.The translation messages object. Use the Messages type from @nuxt/ui for type safety.

**Returns:** A `Locale<M>` object that can be passed to the `locale` prop of the [App](https://ui.nuxt.com/docs/components/app) component.

## Example

Here's a complete example of creating a custom locale:

```vue
<script setup lang="ts">
import type { Messages } from '@nuxt/ui'

const locale = defineLocale<Messages>({
  name: 'Español',
  code: 'es',
  dir: 'ltr',
  messages: {
    alert: {
      close: 'Cerrar'
    },
    modal: {
      close: 'Cerrar'
    },
    commandPalette: {
      back: 'Atrás',
      close: 'Cerrar',
      noData: 'Sin datos',
      noMatch: 'Sin resultados',
      placeholder: 'Escribe un comando o busca…'
    }
    // ... other component messages
  }
})
</script>

<template>
  <UApp :locale="locale">
    <NuxtPage />
  </UApp>
</template>
```

> [!NOTE]
> 
> You can look at the [built-in locales](https://github.com/nuxt/ui/tree/v4/src/runtime/locale) for reference on how to structure the messages object.


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
