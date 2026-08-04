# <NuxtAnnouncer>

> The <NuxtAnnouncer> component adds a hidden element to announce dynamic content changes to assistive technologies.

<important>

This component is available in Nuxt v4.4.2+.

</important>

## Usage

Add `<NuxtAnnouncer/>` in your [`app.vue`](/docs/4.x/directory-structure/app/app) or [`app/layouts/`](/docs/4.x/directory-structure/app/layouts) to enable announcing dynamic content changes to screen readers. This is useful for form validation, toast notifications, loading states, and other in-page updates.

```vue [app/app.vue]
<template>
  <NuxtAnnouncer />
  <NuxtRouteAnnouncer />
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

Then use the [`useAnnouncer`](/docs/4.x/api/composables/use-announcer) composable anywhere in your app to announce messages:

```vue [app/pages/contact.vue]
<script setup lang="ts">
const { polite, assertive } = useAnnouncer()

async function submitForm () {
  try {
    await $fetch('/api/contact', { method: 'POST', body: formData })
    polite('Message sent successfully')
  } catch (error) {
    assertive('Error: Failed to send message')
  }
}
</script>
```

## Slots

You can pass custom HTML or components through the announcer's default slot.

```vue
<template>
  <NuxtAnnouncer>
    <template #default="{ message }">
      <p>{{ message }}</p>
    </template>
  </NuxtAnnouncer>
</template>
```

## Props

- `atomic`: Controls if screen readers announce only changes or the entire content. Set to true for full content readouts on updates, false for changes only. (default `true`)
- `politeness`: Sets the default urgency for screen reader announcements: `off` (disable the announcement), `polite` (waits for silence), or `assertive` (interrupts immediately). (default `polite`)

## Differences from `<NuxtRouteAnnouncer>`

<table>
<thead>
  <tr>
    <th>
      Aspect
    </th>
    
    <th>
      <code>
        <NuxtRouteAnnouncer>
      </code>
    </th>
    
    <th>
      <code>
        <NuxtAnnouncer>
      </code>
    </th>
  </tr>
</thead>

<tbody>
  <tr>
    <td>
      <strong>
        Purpose
      </strong>
    </td>
    
    <td>
      Announces route/page changes
    </td>
    
    <td>
      Announces any dynamic content
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>
        Trigger
      </strong>
    </td>
    
    <td>
      Automatic on navigation
    </td>
    
    <td>
      Manual via <code>
        polite()
      </code>
      
      /<code>
        assertive()
      </code>
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>
        Message source
      </strong>
    </td>
    
    <td>
      Page <code>
        <title>
      </code>
    </td>
    
    <td>
      Developer-provided
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>
        atomic default
      </strong>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      <code>
        true
      </code>
    </td>
  </tr>
</tbody>
</table>

<callout>

This component is optional. <br />


To achieve full customization, you can implement your own one based on [its source code](https://github.com/nuxt/nuxt/blob/main/packages/nuxt/src/app/components/nuxt-announcer.ts).

</callout>

<callout>

You can hook into the underlying announcer instance using [the `useAnnouncer` composable](/docs/4.x/api/composables/use-announcer), which allows you to set custom announcement messages.

</callout>

<style>

html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/nuxt/src/app/components/nuxt-announcer.ts)
