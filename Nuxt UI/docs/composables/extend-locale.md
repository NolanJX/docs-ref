---
title: "extendLocale"
description: "A utility to extend an existing locale with custom translations."
canonical_url: "https://ui.nuxt.com/docs/composables/extend-locale"
---
# extendLocale

> A utility to extend an existing locale with custom translations.

## Usage

Use the auto-imported `extendLocale` utility to customize an existing locale by overriding specific properties or messages.

```vue
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

This is useful when you want to:

- Create a regional variant of a language (e.g. `en-AU` from `en`)
- Override specific translations without redefining the entire locale
- Customize component labels for your application

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

`extendLocale<M>(locale: Locale<M>, options: Partial<DefineLocaleOptions<DeepPartial<M>>>): Locale<M>`

Extends an existing locale with the provided options, deeply merging the messages.

#### Parameters

**locale** (`Locale<M>`) *required*: The base locale to extend. Import from @nuxt/ui/locale.

**options** (`Partial<DefineLocaleOptions<DeepPartial<M>>>`) *required*: The properties to override:Override the display name of the locale.Override the ISO code of the locale (e.g. 'en-GB', 'fr-CA').Override the text direction of the locale.Partial messages object to merge with the base locale. Only specify the messages you want to override.

**Returns:** A new `Locale<M>` object with the merged properties.

## Example

Here's an example extending the English locale for an Australian variant:

```vue
<script setup lang="ts">
import { en } from '@nuxt/ui/locale'

const locale = extendLocale(en, {
  name: 'English (Australia)',
  code: 'en-AU',
  messages: {
    colorMode: {
      dark: 'Dark',
      light: 'Light',
      system: 'System'
    },
    selectMenu: {
      search: 'Search…',
      noData: 'No results found',
      noMatch: 'No matching results'
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

> [!NOTE]
> 
> The `extendLocale` utility uses deep merging, so you only need to specify the messages you want to override. All other messages will be inherited from the base locale.


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
